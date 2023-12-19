# 1) CAD to GIS
   **High-level:** creates new GDB and sets it as the workspace, imports CAD data from .dwg to Pro, standardizes field values, and adds the newly created CAD FC’s to the table of contents (TOC)
   
   a) creates a new FGDB and sets it as the workspace GDB  
   b) Runs out-of-the-box CADToGDB GP tool with user defined .dwg file as the input  
   c) Polyline specific field standardization  
   d) Lastly, adds newly created “Point”, “Polyline”, and “Polygon” FC’s to TOC (all outputs of CADToGDB GP tool)

# 2) Feature Translation
   **High-level:** Creates a new FC that adheres to the TDS schema for each target feature (8 total target features… access points, splice enclosures, port connection rows, splitters, poles, conduits, fiber cables/fiber drop cables, and serving areas)
   
#### a) There’s attribute info in the “Point” FC that also needs to be included in the “Polyline” FC… so, this first step gets that done… in sort of a round-a-bout way...
  
> - Create copy of “Point” FC  
> - Join the copied “Point” FC and the “Polyline” FC  
> - Create copy of joined “Polyline” FC so the fields are editable  
> - For fields in tuple, copy values from fields starting with "InputPointCopy_" to fields starting with "Polyline_”  
> - Further field house keeping
             
#### b) Goes into each CAD FC (Point, Polyline, Polygon) and selects for desired features using a select by attribute query
  > if the selection is > 0, create an empty FC in the workspace GDB and append the selected features to it
      
  > - For polylines: runs the densify GP tool on the translated FC

#### c) TOC and GDB clean-up

> - deletes “Point” FC copy
> - removes imported CAD and “Polyline_join” FC’s from TOC

# 3) Append to TDS
**High-level:** Appends the translated feature classes to the TDS feature service

a) specify the name and path of the desired translated FC  
b) if the FC exists in the workspace GDB, append it to the appropriate TDS feature service


# 4) Definition query (DQ) change
**High-level:** changes layer and table definition queries so that only the newly appended features are visible in the map

> - Query uses the “TDS_WBS_CODE” value, which is unique per project
