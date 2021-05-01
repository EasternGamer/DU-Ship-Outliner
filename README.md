# Dual Universe Ship Outliner

This is a "simple" script to enable real-time creation of a ship model in an accessible and easy manor. The goal is to enable anyone to create "exportable" versions of their ship designs. 

Heavily optimized with the help of Elias Villd and his [script](https://github.com/EliasVilld/du-logger), with it intergrated to help with understanding the performance issues.

## Setup

It is recommended you watch the tutorial video [here](notyetdone), but since that's not always possible,  I'll also have a written version.

First, you will need to assign the following ALT+ keys to enable an easier experience. Below you will find what each key does.
Bolded assignments are highly recommended.
|Original|Suggested Assignment|Function|
|-------|--|---|
|Alt-1  | **Numpad Plus** | Adds points you have placed down|
|Alt-2  | **Numpad Minus** | Removes the last point|
|Alt-3  | N/A | Enables/Disables mirror mode (enabled by default)|
|Alt-4  | N/A | Enables/Disables connected mode (disabled by default)|
|Alt-5  | Numpad 8 | Increases your "layer"|
|Alt-7  | Numpad 2 | Decreases your "layer"|
|Alt-8  | N/A | Enables/Disables line mode (disabled by default)|
|Alt-9  | Numpad Enter | Saves your points in array format to the linked screen |

You will need the following: **Programming Board** and a **Display**, along with several **Adjustors S**.
>Note that the core must be linked to the programming board and the display must be linked after the core. Otherwise you will probably run into issues.

# How To Use
It is highly recommended you have multiple Adjustors S. The reason is because elements have a different Z displacement, so your points may not be accurate if you use a different element. They are also small, cheap and easy to place.

To begin, follow the setup process, place your programming board and copy contents the "script.txt" in the above. In game, right click the programming board and hover over the "Advanced" tab, over to the right you will see "Paste Lua configuration from clipboard". This takes in contents of the script and converts it automatically to code.

The next step will be to connect the construct's core to the programming board. It's important that you do this first before connecting the screen.
Once that's done, link any screen to the programming board.

Cool! You're 100% setup. Now, onto actually using it.

Your next step will be activate the programming board. Once activated, go into build mode and start placing adjustors.
However, keep in mind that the **path** taken is done in the order you place the adjustors. So, if you place them in the wrong order, they might created unwanted connections.
Once you've used up all your adjustors, or however many were needed to form your first path, press the Numpad + (Alt-1) key to add those points. Magic! You'll see in real-time your work. You should see a line connecting to the points you placed down.
>If you have connected mode enabled, the final point will connect back to the first point.
If you have mirrored mode enabled, the points you placed down will be mirrored on another layer.
If you have both enabled, the connected mode will be applied on the second layer as well.
If you have line mode enabled, it will select every two adjustors and create a new layer of it. If you have mirror mode anbled with that, you will create two layers each single line you make out of adjustors.

When working with the bottom of a ship, you have two choices.

 1. You accept a minor inaccuracy of 1 block by just placing it facing down.
 2. You move the adjustor into the voxel a little, like 1 voxel in.

The reason for these limitations might be addressed in the future, though.

**Bonus:** You can zoom in and out with your scroll wheel 

### Mirror Mode
Just because Dual Universe doesn't support mirroring doesn't mean I won't! Mirroring is just as it says. It will mirror it along the Y-Axis, in other words flipping the X-axis. This halves your potential work if your ship is symmetrical.

To enable or disable it, simply use the ALT-3.

Deleting when mirrored mode is enabled deletes the last point on both the current and previous layer. Be careful when doing this because there is no "undo" button *yet*.

### Line Mode
As the How To Use said, it works by taking two adjustors at a time and making a new polyline per adjustor, in other words, a new layer. Connected mode does not affect this mode, but it is affected by the mirror mode.
Mirror mode does what mirror mode does and duplicates them with a negative X value.

Use this sparingly since this has a much larger hit on performance, especially when paired with mirrored mode. But don't be too scared to use it. The primary purpose would be for where a path doesn't quite work out or you try avoid doubling over on already made points. It's really up to you how you use this.

### Connected Mode
Connected mode is quite useful for when you're making single, terminating line. Generally, you would not use this with mirroring, however, it can be useful where you have a single object, like a hex or cube, where the final point would be your start point. Normally, without this mode, you would need to add all the points but the final point and then remove all the adjustors and place one adjustor on the first/last point to connect it. However, this simply removes that whole step which can be a major time waster.

### Technicals
#### Projection System
The projection system is a fully simulated, perspective, projection model. It uses three matrices to create a model of your ship in 3D space.
Currently, it has been shown to effortlessly simulate up to 2400 points, and display them, at once before CPU overload. (Roughly 620 layers)

The projection system, by default, takes in your camera's position, heading, roll and pitch, as well as the model's heading, roll and pitch. (No model position currently, however that is discussed soon)

Using that, it creates two matrices, the view and model matrices, the camera and ship matrices, respectively. These "matrices" are just tables for efficiency sake. (3x3 for ship and 4x3 for camera)

The third matrix is a static one which we use to transform it into a perspective view. We only deal with two values in the 4x4 matrix.

In the projection function, we localize all the matrix values that we'll frequently be using multiply with. Note that, in theory, the multiplication is of the transposed matrix, but since we're looking for the best possible performance, we do the entire multiplication by manually, reducing the need of transposal of an array.
The values are multiplied by the point's "x", "y" and "z" value to produce a translated "x" and "y" value. The "w" value is used to see if the point is even in our view (aka, behind the camera) and if it isn't, then it won't add it.

#### Point System
The point system is relatively basic, it's just an array of layers, where layers are an array of points, where a point is an array of x, y, and z.
It looks like this:

    Ship {
       Layer 1 {
         Point 1 {
            x, y, z
         },
         Point 2 {
            x, y, z
         }
       },
       Layer 2 {
         Point 1 {
            x, y, z
         },
         Point 2 {
            x, y, z
         }
       }
    }
    

This will produce two lines between those x, y and z points. Each "layer" is a single "polyline" object, but I've used a single path, technically speaking.

### Other/Remarks
Points marked in white are points not on the current layer, while points marked in green are on the current layer.
There might be some bugs, so feel free to report some to me (EasternGamer#0001).
