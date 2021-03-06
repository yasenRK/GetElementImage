# GetElementImage

Revit C# .NET add-in that exports a list of PNG images of a selected BIM element from a list of specified view angles.

## Task

The goal is to export "images" of all of the family instances from a Revit model.

When we receive a new Revit model from a new project, and we want to quickly "categorize" all the family symbols there and understand what needs to be tracked and in which way.

Currently, we export all the family symbols to RFA files and look at those:

![RFA thumbnails in Windows Explorer](img/windows_explorer_with_rfa_thumbnails.png "RFA thumbnails in Windows Explorer")

Problems:

- We don't want to use the RFA format, but some normal imaging format so it can be viewed in some more friendly system, e.g., export these images to jpg or png format.
- We want to be able to export a specific element instance, both `FamilyInstance` and other element types.
- We want to be able to see the element from multiple angles.  

Another option would be to use some Python code (unrelated to Revit, on a computer where Revit is not installed) and just parse these RFA files.

Some possible way to go with this:

For instance, on one extreme, going away from Windows and the Revit desktop API completely, you could push the RVT model to Forge and isolate the individual family instances there, in the cloud, in the Forge viewer. Two of the advantages with that would be: A. everybody can see them, with zero installation requirements, directly in the browser. B. they can be rotated and zoomed directly in the browser, so you can view them from all angles interactively.

Within Revit, on Windows, with the .NET desktop API, a schedule similar to the one in your image can easily be generated.

Simply create a 3D view, isolate the family instance you wish to create a view of, rotate it into the required position and export a screen snapshot or an image file for it. If required, repeat for other viewing angles. Iterate over all the instances one by one to create all the required views. Put all the exported images together into the schedule as required. Disadvantage: fixed viewing angle.

If you have the gltf exporter up and running and doing what you need, you can also use that directly to generate all the gltf 3D data for all the instances and then render them as needed outside of Revit. In theory, here you could actually put real 3D gltf directly into the schedule and rotate each individual family instance inside its own cell.

We will explore option number 2, exporting the images directly from Revit. 

- This gives enough information.
- Simpler to implement.
- It will be merged into another system, that not necessarily will be web-based.

Usage:

- Pick an element
- Take a picture of it from multiple angles (sides, top, ...)
- Save to PNG file
- Include ability to select other, non-family-instances, elements as well (pipes, ...)
- Use the object dimension to select the distance from the element (currently, just taking its bounding box is enough)

We use a fixed list to define the angles from which to generate the element views.
The definition format doesn't really matter. I would go with:

- Distance from element centre (defined by the bounding box).
- Horizontal angle, 0-360 degrees; 0 means looking North, 180 means looking South. Also might be called "yaw".
- Vertical angle, -90 to +90 degrees, where 0 means being on the same height as the element centre and 90 means looking from the top. Also might be called "pitch".

In all cases, looking directly at the centre of the element (by bounding box centre).

Existing sample code:

Some implementation hints have been described by Alexander Ignatovich and The Building Coder in the article 
on [exporting image and setting a default 3D view orientation](https://thebuildingcoder.typepad.com/blog/2013/08/setting-a-default-3d-view-orientation.html).

Another sample of `ExportImage` option settings is provided in the recent discussion on [export image cutting off pixels](https://thebuildingcoder.typepad.com/blog/2020/06/creating-material-texture-and-retaining-pixels.html#3).

If the background is important, the discussion on [setting the view display background](https://thebuildingcoder.typepad.com/blog/2013/12/setting-the-view-display-background.html) addresses that aspect.

[The Building Coder samples]() includes the `CmdExportImage` sample command that exports an image of a view.

Current implementation:

Currently, a single element is selected.

Separate snapshots of it are exported to PNG bitmaps based on a pre-defined list of view directions.

The views are created and set up from scratch in a transaction that is rolled back and aborted, so no modification is made to the model.

That is very time-consuming.

Possible enhancements:

- Select more than one element to be exported
- Set up all the required views once only and retain them in the model

## <a name="author"></a>Author

Jeremy Tammik, [The Building Coder](http://thebuildingcoder.typepad.com), [ADN](http://www.autodesk.com/adn) [Open](http://www.autodesk.com/adnopen), [Autodesk Inc.](http://www.autodesk.com)

## <a name="license"></a>License

This sample is licensed under the terms of the [MIT License](http://opensource.org/licenses/MIT).
Please see the [LICENSE](LICENSE) file for full details.

