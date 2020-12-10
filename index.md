### Team- Nathan Harding

## Problem

The aim of my project was to create a program to generate a voronoi based infill, which would be run before the model was sent to a slicer. 

## Background

For 3D printing, typically one of the goals is to reduce the amount of material required to print an object, while still maintaining structural integrity under stress.  In addition, the object needs to have enough support to not collapse during printing.  

In order to achieve this, there are multiple patterns called infill paterns, which are used to fill up a shell during printing.  Typically these patterns are simple and repeatable, such as a grid of boxs, stars, or spirals. Usually this is created by a complex program called a slicer, which is responsible for taking in a file describing a 3D object, and outputs a set of instructions called G-code based on a variety of settings.

A voronoi diagram displays the region around each of a set of points, where any area in that region is closer to that point than any other.  We will be working in 3D, which means each region will be created by a set of bounded planes, which we will refer to as ridges.  The bounds on these ridges are determined by a set of points on the plane, which we will refer to as vertices.

## Inputs and Outputs
#### Input
The input for this program consists of a single stl file, describing the model to be filled in terms of the triangles that form its shell.  The file can be in either ascii or binary.
The program itself operates on the list of triangles read in, as well as a list of their normal vectors.
#### Output
The output for this program consists of a single stl file, describing the model hollowed out by polyhedrons as described by a randomly generated voronoi diagram.


## Requirements
The main requirements for this program's output is that the resulting stl file is printable.  This means that all triangles have properly oriented normals and are connected.  Triangles that aren't properly created can have unpredictable results when run through the slicer.  An optimal solution is not required, however an nonoptimal solution will struggle with high-poly models, or models that contain a large number of triangles.

## Related Works
Similar problems to this have been approached multiple times before.

[Efficient Computation of 3D Clipped Voronoi Diagram](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/12/Efficient-Computation-of-3D-Clipped-Voronoi-Diagram.pdf) by Dong-Ming Yan et. al describes an approach to created a bounded Voronoi Diagram. In this approach, a set of points is generated inside of a model, a voronoi diagram is produced, then any ridges which intersect the outer shell are clipped, and the affected regions are modified.

[Build-to-Last: Strength to Weight 3D Printed Objects](https://homes.cs.washington.edu/~haisen/BuildtoLast/3DP_SIG2014.pdf) by Lin Lu et. al describes an approach to creating printable models using machine learning.  In this approach a set of points is generated inside of the model and a voronoi diagram is generated.  The regions in this diagram are hollowed out by carving out the material specified by a harmonic distance field.  The ability of this model to sustain stress is then determined, and then the location of the points is shifted using an iterative approach, seeking to maximize the amount of stress maintained.

## My Approach
I primarily explored two approaches for this method, neither of which were efficient enough for real-world use.  I will focus mainly on the second algorithm in this section.

The algorithm begins with a set of triangles given by the stl, as well as a second list of their normals.  
1. First we create a set of all unique points that occur in the set of triangles, noting their minimum and maximum value in each dimension.  We will use these values to create a bounding box around the model.
2. Next we will generate a single random point, which we will call X, within a bounding box of the model, and create a voronoi diagram using this new point and the set of points we created earlier.
3. Now we examine the region created by X in the voronoi diagram. First we will find all neighboring points from the shell by finding the regions that share a ridge with this one.  For each one of these points, we will locate the original triangles containing that point in the stl.  Based on the inner product of the normals of these triangles and the vector created by moving from one of the triangles to X.  If our point is located within the model, we can move on.  Otherwise we will move back to step 2.
4. Next we will check all of the vertices that make up X's region in a similar fashion as described in 3.  In order to prevent trimming, we will make sure that X's region is also contained entirely within the model.  
5. If the region is contained entirely within the model, we can append X to the list that we generated in 1, and then move on to step 2 again.  We can repeat this for as regions as we want to be hollowed out.
6. The regions created by a voronoi diagram are pefectly packed.  Therefore once we are content with the number of regions, our next goal is to shrink them in order to make them printable.  We accomplish this by iterating through every region that we have created, and calculating the normal at each vertex.  We can approximate this by averaging the normals of each ridge adjacent to the vertex.  Next we shift the point a small amount in the direction opposite its normal, resulting in a slightly smaller shape.  Once this is done for all regions, the end result is a gap between them, which will be printed by the 3D printer.
### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/nah6563/Voronoi3DPrint/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
