There are two ways to use oxyplot.dll to draw plot as you want:

- C# Code Dealing 
- WPF XAML Dealing

#### C# Code Dealing

Advantages：灵活自定义、绘制效率高
>Steps as following:
>
>1、定义PlotModel

>var plotModel = new PlotModel
{
Title = "",
LegendPosition = LegendPosition.TopLeft,
LegendPlacement = LegendPlacement.Outside,
LegendItemAlignment = HorizontalAlignment.Left,
LegendOrientation = LegendOrientation.Horizontal,
DefaultFont = ConstantValues.DefaultFont,
DefaultFontSize = ConstantValues.DefaultFontSize
};

>2、定义轴LinearAxis

>var xAxis = new LinearAxis
{
Position = AxisPosition.Bottom,
Key = "x",
MajorGridlineStyle = LineStyle.Solid
};

>3、将轴添加到PlotModel中

>plotModel.Axes.Add(xAxis);

>4、创建（线条）数据

>var  lineSeries = new LineSeries
{
Title = line.Title,
XAxisKey = xAxis.Key,
YAxisKey = i.ToString(),
Color = OxyColor.Parse(line.Color.ToString()),
LineStyle = line.LineStyle,
TrackerFormatString = "{0}\n{1}:{2}\n{3}:{4:0.00}"
};

>5、将线条添加到PlotModel中

>lineSeries.Points.AddRange(line.Points.Select(p =>new DataPoint(p.X,p.Y)));
plotModels.Series.Add(lineSeries);

#### WPF XAML Dealing
Advantages：简单方便、类实时绘制
