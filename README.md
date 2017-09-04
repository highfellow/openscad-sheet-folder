# OpenSCAD Sheet Folder

This will be a module for OpenSCAD which lets you design models based on folded sheet materials.

## Notes

Usage:
- Create the 2D layout for the sheet using whatever OpenSCAD operations you like. This is the initial input to the folding module -- i.e. you design the sheet un-folded and then fold it.
- You also define fold lines which will be used to fold the sheet.
- A global special variable, `$fold` defines whether to fold the sheet or not. If this is true, the folds will be carried out and the sheets made to the desired thickness. If it is false, the design will be left as 2D.
- A fold line has a start point, end point, an angle to fold by, and a width to fold over. 
- The region to fold over is found by moving from the start point to the centre of the fold, turning right by 90 degrees, displacing the fold line along the normal by the fold width, and creating a rectangle from the two lines. This region must be rectangular in the 2D design or the module won't work.
- After folding, the region of the sheet to the 'left' of the fold line looking from the start point to the end is left where it is, the region of folding is replaced with an angular section of a hollow cylinder with the same wall thickness as the sheet and the region to the 'right' of the fold line is translated and rotated to its new location *along* *with* all the fold lines that apply to the folded region.
- This process is repeated recursively for each of the fold lines.
- The pieces created like this are put together using Boolean unions.
- A $delta variable defines how much overlap there is between objects so the booleans work properly.

Design:
- a tree-like data structure is probably right for specifying the fold lines. Maybe a vector containing other vectors? 
- it would be nice to be able to create this using named operators applied to the 2D sheet, but I am not sure yet how this could work in OpenSCAD. A vector of vectors would work but is not that readable.
- can modules operate on a data structure as well as a geometric object?
- maybe you could generate the vector of vectors specifying the cuts through a set of nested calls to a custom function. The function would take parameters of cut line, angle, width, and a vector of child cuts, which would be created through calls to the same function.
- actually cutting a 2D object along a line seems to be an issue in itself. Have asked on the OpenSCAD forum about this.
- the sheet needs to be extruded to 3D *after* the folds have all been done. I.e. you start with the 2D sheet, then apply all the folds in succession, and then extrude the sheets and add the bent sections.
