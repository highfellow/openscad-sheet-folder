# OpenSCAD Sheet Folder

This will be a module for OpenSCAD which lets you design models based on folded sheet materials.

## Notes

Usage:
- Create the 2D layout for the sheet using whatever OpenSCAD operations you like. This is the initial input to the folding module - i.e. you design the sheet un-folded and then fold it.
- You also define fold lines which will be used to fold the sheet.
- A global special variable, `$fold` defines whether to fold the sheet or not. If this is set to 'fold', the folds will be carried out and the sheets made to the desired thickness. If it is set to 'debug', the design will be left as 2D and different coloured regions added to the model to show the boundaries of the folds. If it is set to 'output', the design will be returned unmodified for output to a laser cutter or other CNC device. 
- A fold line has a start point, end point, an angle to fold by, and a width to fold over. 
- The region to fold over is found by moving from the start point to the centre of the fold, turning right by 90 degrees, displacing the fold line along the normal by the fold width, and creating a rectangle from the two lines. This region must be rectangular in the 2D design or the module won't work.
- After folding, the region of the sheet to the 'right' of the fold line looking from the start point to the end is left where it is, the region of folding is replaced with an angular section of a hollow cylinder with the same wall thickness as the sheet and the region to the 'left' of the fold line is translated and rotated to its new location *along* *with* all the fold lines that apply to the folded region.
- This process is repeated recursively for each of the fold lines.
- The pieces created like this are put together using Boolean unions.
- A `$delta` variable defines how much overlap there is between objects so the booleans work properly.

Design:
- a tree-like data structure is probably right for specifying the fold lines, so you can have folded regions with sub-folds in them. Maybe a vector containing other vectors? This would not be that readable though.
- maybe you could generate the vector of vectors specifying the cuts through a set of nested calls to a custom function. The function would take parameters of cut line, angle, width, and a vector of child cuts, which would be created through calls to the same function. The top level function would return the fold tree vector as its result.
- actually cutting a 2D object along a line seems to be an issue in itself. Have asked on the OpenSCAD forum about this.
- it might be possible to do the actual cutting and bending using a custom module that takes the fold tree as one parameter, the sheet thickness as another, and the unfolded 2D model as its first child. This could call itself recursively to do the cutting and bending? Each stage of the recursion would return an object as its only child which contained a part with all the folds at that level of the tree applied. At a given level the module might call itself several times for each of the sub folds. Each of these calls would return a folded sub-sheet. These sub-sheets would then be used by the higher level instance of the module to make the next level up and so on.
