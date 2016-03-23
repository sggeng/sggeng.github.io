---
layout: post
title: "XAML FlipView Virtualization Control"
date:   2015-05-01 12:20:00
meta: "FlipView Virtualization is very handy.  Until it is not."
categories: blog
tags: misc
featured: true
image: xaml.jpg
---
In XAML, the `FlipView` control is really handy for content rendering. Out of the box, you get support for virtualization.  For example, when binding a collection of 100 items to a `FlipView`, the control won't build out 100 pages of controls. Instead, only a "window" will be rendered (typically the current item, along with the previous and next item).  While this is great for performance and optimization when binding to a large collection, issues can arise. 

In a Windows Store app project I'm involved with, the end user can mark up content (which is captured in a canvas with shapes rendered from their gestures).  Issues began to arise when virtualization came into play, along with necessary `async` actions.  Here are a few of the challenges and some solutions to address them.

###Control State Hanging Around
With a typical `FlipView`...

{% highlight xml %}
<Page
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    x:Name="pageRoot">
<FlipView ItemsSource="{Binding Images}" SelectedItem="{Binding CurrentImage, Mode=TwoWay}">
 <FlipView.ItemsPanel>
        <ItemsPanelTemplate>
            <VirtualizingStackPanel Orientation="Horizontal" />
        </ItemsPanelTemplate>
    </FlipView.ItemsPanel>
    <FlipView.ItemTemplate>
        <DataTemplate>
            <controls:DrawingView
                Image="{Binding CurrentImage}"
                DrawingViewModel="{Binding DataContext.DrawingViewModel, ElementName=pageRoot}"/>
        </DataTemplate>
    </FlipView.ItemTemplate>
</FlipView>
</Page>
{% endhighlight %}

...with a defined template in a resource...

{% highlight xml %}
<ResourceDictionary
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:Controls">
   <Style TargetType="local:DrawingView">
        <Setter Property="Template">
            <Setter.Value>
                <ControlTemplate TargetType="local:DrawingView">
                    <Border x:Name="LayoutRoot"
                        Background="{TemplateBinding Background}"
                        BorderBrush="{TemplateBinding BorderBrush}"
                        BorderThickness="{TemplateBinding BorderThickness}">
                        <ScrollViewer x:Name="ScrollViewer" MinZoomFactor="1" HorizontalScrollBarVisibility="Hidden" VerticalScrollBarVisibility="Hidden">
                            <Grid x:Name="Container">
                                <Image Source="{TemplateBinding Image}"/>
                                <Viewbox Stretch="Uniform">
                                    <local:DrawingCanvas x:Name="DrawingCanvas"
                                                            DataSource="{TemplateBinding DataContext}"
                                                            DataContext="{TemplateBinding DrawingViewModel}"
                                                            />
                                </Viewbox>
                            </Grid>
                        </ScrollViewer>
                    </Border>
                </ControlTemplate>
            </Setter.Value>
        </Setter>
    </Style>
</ResourceDictionary>
{% endhighlight %}

...the default behavior is to recycle the controls used to render each "page" in the `FlipView`.  This is good for performance. Bad, when each instance can be uniquely marked up by the user.  As the user flips thru their collection, the markings stored in the canvas will re-appear on different views (as the runtime "recycles" the controls and re-uses them, along with their existing state).

A quick fix for this is to change the default property of `Recycling` to `Standard`, which will cause the virtualization to create new instances on demand (rather than reuse instances).

{% highlight xml %}
<FlipView ItemsSource="{Binding Images}" SelectedItem="{Binding CurrentImage, Mode=TwoWay}">
    <FlipView.ItemsPanel>
        <ItemsPanelTemplate>
            <VirtualizingStackPanel Orientation="Horizontal" VirtualizationMode="Standard" />
        </ItemsPanelTemplate>
    </FlipView.ItemsPanel>
    <FlipView.ItemTemplate>
        <DataTemplate>
            <controls:DrawingView
                Image="{Binding CurrentImage}"
                DrawingViewModel="{Binding DataContext.DrawingViewModel, ElementName=pageRoot}"/>
        </DataTemplate>
    </FlipView.ItemTemplate>
</FlipView>
{% endhighlight %}

###Persistence Hooks
Another issue arises when needing to persist the user's drawings to the file system as they flip thru their content.  The first thought is to use the load and unload events.  However, when these fire, the content is gone (making persisting a challenge, to say the least).  Another option is to wire up to the selection change events on the `FlipView`, like so.

{% highlight xml %}
<Page
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:Interactivity="using:Microsoft.Xaml.Interactivity"
    xmlns:Core="using:Microsoft.Xaml.Interactions.Core">
<FlipView ItemsSource="{Binding Images}" SelectedItem="{Binding CurrentImage, Mode=TwoWay}">
    <FlipView.ItemsPanel>
        <ItemsPanelTemplate>
            <VirtualizingStackPanel Orientation="Horizontal" VirtualizationMode="Standard" />
        </ItemsPanelTemplate>
    </FlipView.ItemsPanel>
    <FlipView.ItemTemplate>
        <DataTemplate>
            <controls:DrawingView
                Image="{Binding CurrentImage}"
                DrawingViewModel="{Binding DataContext.DrawingViewModel, ElementName=pageRoot}"/>
        </DataTemplate>
    </FlipView.ItemTemplate>
    <Interactivity:Interaction.Behaviors>
        <Core:EventTriggerBehavior EventName="SelectionChanged">
            <Core:InvokeCommandAction Command="{Binding DataContext.DrawingViewModel.SelectionChangedCommand, ElementName=pageRoot}" InputConverter="{StaticResource SelectionChangedConverter}" />
        </Core:EventTriggerBehavior>
    </Interactivity:Interaction.Behaviors>
</FlipView>
{% endhighlight %}

Simply build and wire up a converter to pass around the instances you need, like in the following quick example.

{% highlight csharp %}	
public class SelectionChangedConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, string language)
    {
        if (value is SelectionChangedEventArgs)
        {
            var eventArgs = (SelectionChangedEventArgs)value;

            return new DrawingSelectionChangedEventArgs(eventArgs.AddedItems.FirstOrDefault() as IDrawingDataSource,
                                                        eventArgs.RemovedItems.FirstOrDefault() as IDrawingDataSource);
        }

        return null;
    }

    public object ConvertBack(object value, Type targetType, object parameter, string language)
    {
        throw new NotImplementedException();
    }
}
{% endhighlight %}	

However, another issue still exists.  When performing an `async` save to the file system (or other persistence source), the unload process will continue to happen along side the `async` calls (given the nature of being `async`).  When trying to work with controls and get the necessary data to persist (for example, the canvas and anything added by the user), the control hierarchy will be blown away underneath you.  We want our persistence to stay `async`, since blocking the UI as the user flips content will generate a bad experience.  Since we can't `await` the events fired by the `FlipView`, another option is to ensure the instance isn't unloaded during virtualization.  Luckily, a hook exists for that by wiring up to the `CleanUpVirtualizedItemEvent` on the `VirtualizingStackPanel`.

{% highlight xml %}	
<FlipView ItemsSource="{Binding Images}" SelectedItem="{Binding CurrentImage, Mode=TwoWay}">
    <FlipView.ItemsPanel>
        <ItemsPanelTemplate>
            <VirtualizingStackPanel Orientation="Horizontal" CleanUpVirtualizedItemEvent="CleanUpVirtualizedItemEventHandler" VirtualizationMode="Standard" />
        </ItemsPanelTemplate>
    </FlipView.ItemsPanel>
    <FlipView.ItemTemplate>
        <DataTemplate>
            <controls:DrawingView
                Image="{Binding CurrentImage}"
                DrawingViewModel="{Binding DataContext.DrawingViewModel, ElementName=pageRoot}"/>
        </DataTemplate>
    </FlipView.ItemTemplate>
    <Interactivity:Interaction.Behaviors>
        <Core:EventTriggerBehavior EventName="SelectionChanged">
            <Core:InvokeCommandAction Command="{Binding DataContext.DrawingViewModel.SelectionChangedCommand, ElementName=pageRoot}" InputConverter="{StaticResource SelectionChangedConverter}" />
        </Core:EventTriggerBehavior>
    </Interactivity:Interaction.Behaviors>
</FlipView>
{% endhighlight %}

In this event, we can indicate that the instance shouldn't be cleaned up (based on whatever conditions we need).  For example, if persistence hasn't completed yet.

{% highlight csharp %}	
public void CleanUpVirtualizedItemEventHandler(object sender, CleanUpVirtualizedItemEventArgs e)
{
	var item = e.UIElement as FlipViewItem;
	if (item != null)
	{
		var canvas = VisualTree.GetChildControl<DrawingCanvas>(item);
		
		if (item.IsSelected)
			e.Cancel = true;
		
		if (canvas.IsPendingSave)
			e.Cancel = true;
	}
}
{% endhighlight %}

Leveraging and maximizing virtualization is very helpful when building an app with great performance.  After troubleshooting and fine-tuning a solution, perhaps these tips will be helpful to you as well :-)
