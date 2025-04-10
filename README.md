# Implementing Drill-Down Charts in Blazor for Detailed Data Exploration

Follow these steps to implementing the drill-down in chart using [Syncfusion Blazor Chart](https://www.syncfusion.com/blazor-components/blazor-charts).

**Step 1: Define and Populate Data to the Chart**

Define a PopulationData class with two properties:

- **Drilldown**: The name of the region (e.g., *Asia*, *Africa*)
- **Population**: The estimated population value for 2025

```
public List<PopulationData> DataSource { get; set; } = new List<PopulationData>
{
    new PopulationData { Drilldown = "Asia", Population = 4849336830},
    new PopulationData { Drilldown = "Africa", Population = 1567367367 },
    new PopulationData { Drilldown = "Europe", Population = 744010306 },
    new PopulationData { Drilldown = "America", Population = 1058663026 },
    new PopulationData { Drilldown = "Oceania", Population = 46867762 }
};
public class PopulationData
{
    public double Population { get; set; }
    public string Drilldown { get; set; }
}

```
**Step 2: Configure Chart Component**

The chart includes one series that binds directly to our population data. The XName is set to Drilldown, which represents each continent, and the YName is set to Population to show the corresponding values.
For better data visibility, data labels are enabled and positioned outside the bars. Event handlers such as [OnPointClick](https://blazor.syncfusion.com/documentation/chart/events#onpointclick), [OnAxisLabelClick](https://blazor.syncfusion.com/documentation/chart/events#onaxislabelclick), [OnPointRender](https://blazor.syncfusion.com/documentation/chart/events#onpointrender), and [OnDataLabelRender](https://blazor.syncfusion.com/documentation/chart/events#ondatalabelrender) are wired up using the [ChartEvents](https://help.syncfusion.com/cr/blazor/Syncfusion.Blazor.Charts.ChartEvents.html) tag.


```
<div class="control-section" align='center'>
    <SfChart @ref="ChartObj" Title="@ChartTitle" SubTitle="@ChartSubTitle" Width="@Width" UseGroupingSeparator="true">

….
        <ChartEvents OnPointRender="PointRender" OnPointClick="OnPointClick" OnAxisLabelClick="AxisLabelClick" OnDataLabelRender="DataLabelRender">
        </ChartEvents>
….
        <ChartSeriesCollection>
            <ChartSeries Fill="@Fill" DataSource="@DataSource" Name="Population" XName="Drilldown" Width="2" Opacity="1"
                         YName="Y" Type="ChartSeriesType.Column"
                         CornerRadius="@(new ChartCornerRadius { TopLeft = 5, TopRight = 5})">
                <ChartMarker>
                    <ChartDataLabel Visible="true" Position="LabelPosition.Outer"></ChartDataLabel>
                </ChartMarker>
            </ChartSeries>
        </ChartSeriesCollection>
….
    </SfChart>
</div>

```

**Step 3: Handling Click on Data Points**

The [OnPointClick](https://blazor.syncfusion.com/documentation/chart/events#onpointclick) event plays a crucial role in handling these interactions. When a user clicks on a data point, this event method is triggered. Within it, each case in the switch statement corresponds to a continent. Based on the clicked point index, the chart title, breadcrumb region name, and data source are updated to display the population details of the selected region.

```
public void OnPointClick(PointEventArgs args)
 {
     if (args.Point.Index != 6)
     {
         if (!clicked)
         {
             TextDecoration = "none";
             Interval = Double.NaN;
             Fill = Colors[args.Point.Index % 10];
             IsDivVisible = true;
             switch (args.Point.Index)
             {
                 case 0:
                     clicked = true;
                     ChartTitle = "Top Populated Countries of Asia - 2023";
                     RegionName = "Asia";
                     DataSource = new List<PopulationData>
                     {
                         new PopulationData { Y = 1416096094, Drilldown = "China" },
                         new PopulationData { Y = 1463865525, Drilldown = "India" },
                         ...
                     };
                     break;
                    
                     ...

             }
         }
         else
         {
             ChartObj.PreventRender();
         }
     }
 }

```

**Step 4: Handling Click on Axis Label**

The [OnAxisLabelClick](https://blazor.syncfusion.com/documentation/chart/events#onaxislabelclick) event updates the chart in the same way as the OnPointClick event, using the index of the selected label provided in the event argument. After making the necessary changes, StateHasChanged is called to refresh the UI, ensuring that the chart is immediately updated with the new data.

```
public void AxisLabelClick(AxisLabelClickEventArgs args)
 {
     if (args.Index != 6)
     {
         if (!clicked)
         {
             TextDecoration = "none";
             Fill = Colors[args.Index % 10];
             IsDivVisible = true;
             Interval = Double.NaN;
             switch (args.Index)
             {
                 case 0:
                     clicked = true;
                     ChartTitle = "Top Populated Countries of Asia - 2023";
                     RegionName = "Asia";
                     DataSource = new List<PopulationData>
                     {
                         new PopulationData { Y = 1416096094, Drilldown = "China" },
                         new PopulationData { Y = 1463865525, Drilldown = "India" },
                        ...
                     };
                     break;
                    
                     ....
             }
             StateHasChanged();
         }
     }
 }

```

**Step 5: Reset to Default View**

The `NavigateToDefault` method restores the default view, displaying the overall population distribution across continents.

```
private void NavigateToDefault()
{
    if (clicked)
    {
        TextDecoration = "underline";
        Color = "blue";
        clicked = false;
        Interval = 1000;
        RegionName = "";
        IsDivVisible = false;
        ChartTitle = "Top Populated Continents of 2023";
        DataSource =
        new List<PopulationData>
            {
            new PopulationData { Drilldown = "Asia", Y = 4778 },
            new PopulationData { Drilldown = "Africa", Y = 1481 },
            new PopulationData { Drilldown = "Europe", Y = 746 },
            new PopulationData { Drilldown = "North America", Y = 379 },
            new PopulationData { Drilldown = "Oceania", Y = 46 }
            };
        ChartObj.PreventRender(false);
    }
    StateHasChanged();
}

```

**Live Demo**
