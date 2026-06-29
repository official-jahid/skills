---
name: windows-desktop-app
description: Windows desktop application patterns - WinForms, WPF, Electron, MSI/EXE deployment, and Windows integration.
license: MIT
metadata:
  author: csharp-skills
  version: "1.0.0"
---

# Windows Desktop Application (C#)

Desktop application patterns using WinForms, WPF, and Electron.NET.

## WinForms Setup

```csharp
using System.Windows.Forms;

public class MainForm : Form
{
    private Button button;
    
    public MainForm()
    {
        Text = "My App";
        Size = new Size(800, 600);
        
        button = new Button { Text = "Click me" };
        button.Click += Button_Click;
        Controls.Add(button);
    }
    
    private void Button_Click(object sender, EventArgs e)
    {
        MessageBox.Show("Hello World!");
    }
}
```

## WPF Setup

```xml
<!-- MainWindow.xaml -->
<Window x:Class="MyApp.MainWindow" Title="App">
    <Grid>
        <Button Content="Click me" Click="Button_Click"/>
    </Grid>
</Window>
```

```csharp
// MainWindow.xaml.cs
private void Button_Click(object sender, RoutedEventArgs e)
{
    MessageBox.Show("Hello World!");
}
```

## Electron.NET

```csharp
using ElectronNET.API;

public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        if (HybridElectron.HasValue && HybridElectron.Value)
        {
            Electron.WindowManager.CreateWindowAsync();
        }
    }
}
```

## MSI Deployment with WiX

```xml
<Wix xmlns="http://schemas.microsoft.com/wix/2006/wi">
  <Product Id="*" Name="MyApp" Language="1033" Version="1.0.0.0">
    <Package InstallerVersion="200" Compressed="yes" />
    <Media Id="1" Cabinet="media1.cab" EmbedCab="yes" />
    
    <Directory Id="TARGETDIR" Name="SourceDir">
      <Directory Id="ProgramFilesFolder">
        <Directory Id="INSTALLFOLDER" Name="MyApp" />
      </Directory>
    </Directory>
    
    <Feature Id="Complete" Level="1">
      <ComponentGroupRef Id="ProductComponents" />
    </Feature>
  </Product>
</Wix>
```

## ClickOnce Deployment

```xml
<PropertyGroup>
  <PublishUrl>http://mysite.com/app/</PublishUrl>
  <UpdateEnabled>true</UpdateEnabled>
  <UpdateMode>Foreground</UpdateMode>
</PropertyGroup>
```