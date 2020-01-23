# DataJuggler.Blazor.Components
This is eventually going to include many components, but for now I am starting with a Progress Bar in its early stages.

The ProgressBar is a work in progress and more of a prototype at this point, but it is ready for demo projects or samples.

Below is a quick sample to get you started using it.

Install Package DataJuggler.Blazor.Components

# Directly on a .razor page or component:
@using DataJuggler.Blazor.Components
@using DataJuggler.Blazor.Components.Interfaces;

# Partial Class (code behind)
using Microsoft.AspNetCore.Components;
using DataJuggler.Blazor.Components;
using DataJuggler.Blazor.Components.Interfaces;

# Component in a razor app
<ProgressBar Subscriber=this Increment="5" Interval="50"
    Continuous="false" HideWhenFinished="true"></ProgressBar>
    
The hosting page or component must implement the IProgressSubscriber interface.

# Implementing IProgressSubscriber Interface
This interface contains two simple methods and one property.

public partial class Index : IProgressSubscriber
{
   ...
}

# IProgressSubscriber Interface Methods

# Refresh

    /// <summary>
    /// This method is called by the ProgressBar when as it refreshes.
    /// </summary>
    public void Refresh(string message)
    {
        // your UI can response to this message if needed. 
    }

# Register

    /// <summary>
    /// This method Registers the ProgressBar with this app.
    /// </summary>
    public void Register(ProgressBar progressBar)
    {
        // store the ProgressBar
        this.ProgressBar = progressBar;
    }
    
# ProgressBar Property

This property is stored after the Register method is called by the ProgressBar.

    /// <summary>
    /// This property gets or sets the value for 'ProgressBar'.
    /// </summary>
    public ProgressBar ProgressBar { get; set; }

The IProgressSubscriber interface allows the ProgressBar and its host to talk to each other.

# BlazorStyled Setup
Blazor Styled is a great component and the code and documentation are available here:

https://github.com/chanan/BlazorStyled

Read the Server Side Install here for more information:

https://blazorstyled.io/server-side-install

Here are my paraphrase of the instructions for quick setup:
1. Add Nuget Package BlazorStyled to your project. It should be installed with DataJuggler.Blazor.Components, but it still may need to be installed. 
2. Open Startup.cs and in the ConfigureServices method, add this line:

    services.AddBlazorStyled();

3. Open Imports.razor and add the following:
@using DataJuggler.Blazor.Components
@using BlazorStyled

4. Open (underscore)Host.cshtml and add this line at the bottom of the Head section:
@(await Html.RenderComponentAsync<BlazorStyled.ServerSideStyled>(RenderMode.ServerPrerendered))

The documentation of BlazorStyled may still be out of date as Visual Studio changed RenderComponentAsync method since the BlazorStyled docs were written. I have been told this will be fixed.

That should be all thats required to get BlazorStyled configured. I will update this page if I can find a way to automate this part.

# Use Cases

For now, the progress bar is meant to show the user something is happening during a long running process.

The sample project demonstrates using a Timer and on every refresh the progress bar increases the fill width by the increment value
in pixels, up to the Max value. To use the timer, call the Start method on the Progress bar.
In theory, and I will update this once I know it works, you should also be able to manually increase the FillWidth value, which in turn sets the FillWidthPixels string value used by BlazorStyled.

Example: FillWidth = 100
         FillWidthPixels = 100px
         
The FillWidthPixels is used by the CSS for the inner graph.

# Progress Bar Events

    private void Timer_Elapsed(object sender, ElapsedEventArgs e)
    {
       ...
    }
    
This is the Tick event the Timer calls on every cycle. This method increments the FillWidth by the Increment value.
If the value for Max is encountered, either the app will close if HideWhenFinished is true or reset if continuous is true.

# ProgressBar Methods

# Init

This method is called by the Constructor of the ProgressBar and sets the DefaultValues.

# Start

Call this method to Start the timer. This is used where the graph increments on a regular basis based upon the interval set.

public void Start(int startAtValue = 0)

# StartAtValue
This optional parameter defaults to zero, but can be set manually to resume a long process perhaps.

# Stop

This method stops the timer and all future events (already in progress events may finish before stopping)

# ProgressBar Properties

# Continuous

    [Parameter]
    public bool Continuous { get; set; }
    
If true, the progressbar will reset back to zero if the Max value is encountered.

# CurrentValue

    [Parameter]
    public int CurrentValue { get; set; }
    
The CurrentValue also sets the FillWidth. This property might be removed as FillWidth controls FillWidthPixels, and is what the display goes by.

# Display

    public string Display { get; set; }
    

The display is managed by the ProgressBar.Visible property, but you can change it if needed (I think).

Visible = true - Display = inline-block
Visible = false - Display = none

From Progressbar.razor:
display: @Display;

# FillWidth

    public int FillWidth { get; set; }

The FillWidth is the number of pixels displayed up to the Max value.

Setting the FillWidth sets the FillWidthPixels CSS Property.

FillWidth = 90;
FillWidthPixels = 90px

# FillWidthPixels

    public int FillWidthPixeels { get; set; }

As described above, a property that is the CSS value for how many pixels to display.
Set this property by setting the FilWidth integer value else you are in uncharted waters.

# HasSubscriber

    public bool HasSubscriber { get; }

This read only property returns true if the ProgressBar has a Subscriber.

# HideWhenFinished

    [Parameter]
    public bool HideWhenFinished { get; set; }
    
If true, the ProgressBar will close when this value reaches the Max

# Increment

    [Parameter]
    public bool Increment { get; set; }
    
This value sets how many pixels will be added to FillWidth value on every timer event.
Setting this value to higher makes this progress bar fill faster.

The default value is 1 if not set.

# Interval

    [Parameter]
    public int Interval { get; set; }
    
This is the value in milliseconds for how often the Timer event will fire. The default value is 100 if not set.

# Max

    [Parameter]
    public int Max { get; set; }
    
This is the maximum value the ProgressBar inner fill can go to.
The Default Value is 552.

# NotificationInProgress

    public bool NotificationInProgress { get; set; }
    
This property is used internally by the ProgressBar to make sure only one thread of notifications is sent to the Client at a time.
This is also useful for debugging as it keeps the message chain down to single threads.

# PercentFill

    public int PercentFill { get; set; }
    
# This property has not been implemented yet. It may be used in a future version.

# PercentFillStyle

    public string PercentFillStyle { get; set; }
    
This property is the string that is bound to the BlazorStyled CSS property for the innerfill.

# Position

    public string Position { get; set; }
    
This property is set on the BlazorStyled CSS Class for position. Fixed, Absolute and Relative are the 3 I know, there may be more.

# ProgressBackground

    public string ProgressBackground { get; set; }
    
This is the string property bound to the BlazorStyled styles for the ProgressBar div.
In future versions I imagine themes or other styles, or even an option to display the innter graph without the background.

<img src="https://github.com/DataJuggler/DataJuggler.Blazor.Components/blob/master/wwwroot/Images/RedProgressBase.png">

# Scale

I added a double value for Scale that allows to control how big the ProgressBar displays. The default is .5.

# Started

    public bool Started { get; set; }

This property is managed internally the by the ProgressBar when Start and Stop are called.

# Subscriber

    [Parameter]
    public IProgressSubscriber Subscriber

This property registers the parent with the ProgressBar, which enables the ProgressBar to register with the host.

    <ProgressBar Subscriber=this></ProgressBar>

# Timer

    public Timer Timer { get; set; }

The System.Timer Timer that is started when the Start method is called.

# Visible

    [Parameter]
    public bool Visible { get; set; }
    
This property sets the @Display value to either inline-block if true (visible), or none if false (invisible).

# Sprite Component

I created a new Sprite component that allows you to set properties for images.

# Sprite Methods

See the Progress Bar Methods above as they are exactly the same except the StartAtValue does not exist.

# Sprite Properties

Many of the properties are identical to the ProgressBar, only the differences are listed here.

# Height

    [Parameter]
    public int Height { get; set; }

The height in pixels.

# HeightPixels

    public string HeightPixels { get; set; }
    
This value is set by the setter for Height. The string px is appended to the end.

Example: Height: 80
HeightPixels: 80px.

# ImageUrl

    [Parameter]
    public string ImageUrl
    
 This value is set as the background image for the Div.

# Name
 
    [Parameter]
    public string Name
    
The name helps distinquish Sprites from other Sprites.

# SpriteStyle

    public string SpriteStyle { get; set; }
    
This property is used as the CSS class for BlazorStyle.

# Width

    [Parameter]
    public int Width { get; set; }
    
This property sets the WidthPixels property, which in turns sets the Width of the component.

# WidthPixels

    public string WidthPixels { get; set; }
    
This value is set when you set the Width property.

Example: <br>
Width: 900
WidthPixels: 900px;

Most of the other properties should be the same as the ProgressBar.


    
    








