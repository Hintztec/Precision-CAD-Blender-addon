# Precision-CAD-Blender-addon
constructed using claude code

Precision CAD for Blender
SolidWorks-style feature modeling in Blender: sketch on the Front/Top/Right planes with typed dimensions, then build the part from a feature tree you can go back and edit. One Blender unit is one millimetre, and inches are available as a display mode.
Install
In Blender 4.2 or newer, open Edit › Preferences › Get Extensions.
Open the drop-down menu (top right) and choose Install from Disk….
Pick `precision_cad-0.5.0.zip`.
In the 3D View, press N and open the Precision CAD tab.
Quick start: a plate with a bolt circle
Click New Part. This sets the units and creates Boss-Extrude1, a 100 × 50 × 20 mm block on the Top plane.
Select Boss-Extrude1 and change it to Circle, 120 mm diameter, 12 mm depth.
Add a Hole. It starts on the top face of the part. Set:
Position X to 45.
Pattern to Circular, Count 6.
That gives six holes on a 90 mm bolt circle.
Add a Cut-Extrude, Circle, 40 mm, Through All, to make the center bore.
Add a Chamfer of 1 mm.
In Part › Mass Properties, pick the material and click the button to get volume and weight.
Every value stays live. Click any feature in the tree and change a number, and the part rebuilds.
Adding features to a model you already have
Select the model in Blender. It can be your own mesh or an imported STL, OBJ or 3MF.
In the Precision CAD tab, click Make Part from Mesh.
Pick the units the model was built in:
Metres for most Blender models.
Millimetres for most STL files.
The dialog shows the resulting size so you can check it against the real part.
The model becomes Imported1, the first item in the tree. Add cuts, holes and bosses with + as usual.
To put a feature on a particular spot:
Turn on snapping and Shift + right-click the model to drop the 3D cursor on it.
Select the feature, pick its plane, and click Place at 3D Cursor. The sketch plane and center move to that spot.
A few things to know:
Leaky models are allowed. If the model isn't watertight (it has holes or open edges), the add-on says so and switches that part to the Exact solver. Cuts may still misbehave until the mesh is closed. Blender's 3D-Print Toolbox add-on can find and fix leaks.
Rotation and scale are baked in. By default they're applied to the mesh so the dimensions are true. The model's origin stays where it was and acts as the part origin, so the Top, Front and Right planes pass through it.
The original is kept. A copy of the original mesh is saved with the file, and Finish Part cleans it up.
Filleting only the edges you choose
Add a Fillet or Chamfer and set Edges to Picked Edges.
Click Pick Edges. You'll see the part as it is just before this feature, in Edit Mode.
Select what to round:
Click edges, Shift+click to add more.
Alt+click to select a whole loop, like the rim of a hole.
Select faces to round every corner edge around them. Flat edges inside a face are skipped.
Click Done in the Precision CAD tab. Click Cancel to keep the old picks.
The fillet updates live when you change its radius. Re-pick Edges reopens with your earlier picks already selected. The 🔍 button (Check Picks) counts how many picked edges still exist after you change dimensions. If some went missing, re-pick them or raise Match Tolerance.
Threads with Helix
A thread is a Revolve with Helix turned on. The profile spins around the axis and climbs by one Pitch per turn, for the Length you set.
Example: an M10 × 1.5 bolt, 15 mm of thread.
Make the shaft as a Boss-Extrude: Circle, 8.2 mm diameter (about the thread's minor diameter), 20 mm deep.
Add a Revolve with these settings:
Setting	Value
Plane	Front
Profile	Points: `3.9,0.05; 5,0.75; 3.9,1.45`
Axis	Vertical, Axis Position 0
Helix	On
Pitch	1.5
Length	15
That profile is a V that peaks at 5 mm radius, the 10 mm major diameter. It starts at 3.9 mm, slightly inside the 4.1 mm shaft, so the two fuse cleanly.
For a nut or tapped hole, use the same V but tick Cut, with the profile pointing outward from a hole bored to the minor diameter.
Rules the helix checks for you:
The profile must sit clear of the axis.
The profile must be shorter than the pitch along the axis, otherwise neighbouring turns would overlap.
Left-Hand reverses the twist.
Flip Direction makes it climb the other way.
3D-printing tips:
Threads under about M6 rarely print cleanly on an FDM printer.
Print with the thread axis vertical.
Add about 0.2 to 0.4 mm of clearance between mating parts. Make the nut's thread slightly larger, or the bolt's slightly smaller.
Features
Feature	What it does
Boss-Extrude	Adds material. End condition can be Blind or Mid Plane, with Reverse.
Cut-Extrude	Removes material. End condition can be Blind, Mid Plane or Through All. Cuts go into the part from the sketch plane.
Revolve	Spins a profile about the sketch's vertical or horizontal axis, for any angle. Cut makes it remove material. Helix makes it climb along the axis, for threads and coils.
Hole	Simple, counterbore or countersink hole. Through, or blind with a 118° drill point.
Pattern	Each extrude, cut, revolve or hole can be patterned. Linear patterns take two directions and an angle. Circular patterns take a count, total angle and center.
Fillet / Chamfer	Rounds or bevels either every sharp edge or only the edges you pick.
Mirror	Mirrors the part across the Right, Front or Top plane.
Profiles:
Rectangle, with an optional corner radius
Circle
Polygon
Slot
Points: type coordinates such as `0,0; 40,0; 40,10; 10,10; 10,30; 0,30`
Sketch Object: any closed curve or mesh outline you draw, used in that object's own XY plane
Planes:
SolidWorks plane	Blender plane	Normal
Top	XY	+Z
Front	XZ	toward the front view
Right	YZ	+X
Use the ▲/▼ buttons next to Offset to move a sketch plane onto the part's outer face.
Tree tools: suppress (eye icon), reorder, duplicate, delete. Order matters, just like the rollback bar.
How it works
The first solid feature is the part's own mesh. Each later solid feature is a hidden body parented to the part. Fillet, chamfer and mirror become Bevel or Mirror modifiers. Turn on Part › Show Feature Bodies to see those hidden bodies.
Consecutive cuts and holes share one Boolean modifier, and so do consecutive bosses. That means ten holes in a row cost one Boolean pass, not ten. Each copy in a pattern is its own body, so overlapping copies merge cleanly. For example, counterbores that run into each other clear out the material between them.
Finish Part bakes the part into a plain mesh. This cannot be edited afterward, so it's a one-way step.
Keeping it fast
Leave Boolean Solver on Auto. On Blender 4.5 and newer it uses the Manifold solver, which is many times faster than Exact. The Part panel shows which solver is in use.
Group cuts together in the tree. Bosses and cuts that alternate each need their own Boolean pass.
Drop Circle Segments while designing. 32 is fine for roughing out a part. Raise it again before you export the STL.
Only what you change is recalculated. Editing one feature re-meshes just that feature.
If Manifold gives an odd result, switch to Exact. A sketch outline that crosses itself can trip it up.
Limits in this version
It is mesh-based, not a true B-rep CAD kernel. Round features are faceted. Raise Circle Segments for a truer size. At the default of 64, a 10 mm hole is within about 0.006 mm.
Picked fillet edges are tracked by position. SolidWorks tracks them by topology. If a dimension change moves a picked edge, re-pick it. Check Picks tells you when this has happened.
There are no sketch constraints or relations. Sketches are driven by the numbers you type.
There is no native SolidWorks file import or export.
Use Export STL (mm) to take a part into SolidWorks.
To bring SolidWorks parts into Blender, export them from SolidWorks as STL, OBJ or 3MF.
Tests
```
python3 tests/test_geometry.py     # geometry: watertight solids, exact volumes
python3 tests/test_addon_stub.py   # add-on wiring against a stand-in for Blender
```
