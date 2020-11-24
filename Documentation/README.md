# BIM Collaboration Format v2.2 Technical Documentation
![BCF](https://github.com/BuildingSMART/BCF/blob/master/Icons/BCFicon128.png?raw=true "The BCF logo")

### Terms and Abbreviations

GUID
Globally Unique Identifier: http://en.wikipedia.org/wiki/Globally_Unique_Identifier
IfcGuid
Globally Unique Identifier in the IFC data. This format is used only when referring to objects in IFC datasets.


|       |           |
| ------------- |:-------------|
| BCF      | BIM Collaboration Format |
| BCF file     | File in BIM Collaboration Format     |
| Topic | One topic, such as a problem in the design, described in BCF file    |
| GUID |Globally Unique Identifier: http://en.wikipedia.org/wiki/Globally_Unique_Identifier |
| ServerAssignedId | A server-controlled, user-friendly and project-unique issue identifier. More details in [BCF-API](https://github.com/buildingSMART/BCF-API/tree/release_2_2#429-topic-identifiers) |

### Background
* This document describes the BCF format that is used to exchange topics, such as, issues, scenes, etc. between different BIM software.

### BCF file structure
A BCF file is a zip containing one folder for each topic with its file extension "bcfzip" for BCFv1.0 and BCFv2.0. The file extension "bcf" is introduced since BCFv2.1.
The root of the BCF zip contains the following files.

* project.bcfp (optional)
    - An XML file referencing the extension.xsd to a project. The schema for this file is project.xsd.
* documents.xml (optional)
    - An XML file referencing the documents in a project. The schema for this file is documents.xsd.
* bcf.version
    * An XML file following the version.xsd schema with information of the BCF schema used. The file content should be identical to the contents of [bcf.version](bcf.version "bcf.version")

It is possible to store additional files in the BCF container as documents.
The documents must be located in a folder called `Documents` in the root directory, and must be referenced by the `documents.xml` file.
For uniqueness, the filename of a document in the BCF must be the document guid. The actual filename is stored in the `documents.xml`.

### Topic folder structure inside a BCFzip archive

The folder name is the GUID of the topic. This GUID is in the UUID form. The GUID must be all-lowercase. The folder contains the following files:

* markup.bcf
    * An XML file following the markup.xsd schema that is described below.
* viewpoint.bcfv
    * An XML file following the visinfo.xsd schema that is described below.
    * Multiple viewpoints are possible since BCF 2.0. Names of these files are not predefined. Note: One viewpoint needs to be be named viewpoint.bcfv even in the case of multiple viewpoints.
* snapshot.png 
    *  A snapshot related to the topic (for compatibility with BCF 1.0). The longest dimension of should not exceed 1500 px, length or width.
Multiple snapshots are possible since BCF 2.0. Names of these files are not predefined. Note: One snapshot needs to be named snapshot.png even in the case of multiple viewpoints.

*Note: The elements in the XML files must appear in the order given in the schemas and described below.*

### DateTime Format

DateTime values in this specification are always of type `xs:dateTime` which is an ISO 8601 compatible `YYYY-MM-DDThh:mm:ss` format with optional time zone indicators. This is the same format as defined in the BCF-API specification.

For example, `2016-04-28T16:31:12.270+02:00` would represent _Thursday, April 28th, 2016, 16:31:12 (270ms) with a time zone offset of +2 hours relative to UTC._
Please note that the colon in the timezone offset is optional, so `+02:00` is equivalent to `+0200`.

To void ambiguity, this specification steps away from ISO 8601 on the topic of DateTime values with no timezone: The ISO 8601 says that DateTime values with no timezone designator are local times - **In BCF all DateTime values with no timezone designator are assumed to be in UTC**.

## Project (.bcfp) file

The project file contains reference information about the project the topics belong to.



 Attribute | Optional | Description |
:-----------|:------------|:------------:
 ProjectId  |        Yes |     ProjectId of the project

 In addition it has the following nodes:


 Element | Optional | Description |
:-----------|:------------|:------------
Name | Yes | Name of the project.
ExtensionSchema| No | URI to the extension schema.



## Markup (.bcf) file
The markup file contains textual information about the topic.

### Header
Header node contains information about the IFC files relevant to this topic. Header has also a list of File nodes.

Each File node has the following attributes:


 Attribute | Optional | Description |
:-----------|:------------|:------------
 IfcProject  |        Yes |     IfcGuid Reference to the project to which this topic is related in the IFC file
IfcSpatialStructureElement | Yes | IfcGuid Reference to the spatial structure element, e.g. IfcBuildingStorey, to which this topic is related.
IsExternal | Yes | Is the IFC file external or within the bcfzip. (Default = true).

In addition File has the following nodes:

 Attribute | Optional | Description |
:-----------|:------------|:------------
Filename | Yes | The BIM file related to this topic.
Date | Yes | Date of the BIM file.
Reference | Yes | URI to IfcFile. <br> IsExternal=false “..\example.ifc“ (within bcfzip) <br> IsExternal=true  “https://.../example.ifc“

### Topic
Topic node contains reference information of the topic. It has one required attribute, which is the topic GUID (`Guid`).

 Attribute | Optional | Description |
:-----------|:------------|:------------
Guid | No | Guid of the topic, in lowercase
ServerAssignedId | Yes | A server controlled, user friendly and project-unique issue identifier. Clients provided values will be disregarded by the server |
TopicType | Yes | Type of the topic (Predefined list in “extension.xsd”)
TopicStatus | Yes | Type of the topic (Predefined list in “extension.xsd”)

**Server Assigned ID**

Many server-side implementation follow a long-standing practice of assigning project-specific human-readable IDs to topics for ease of reference; for stability they do not allow users to set or change the value (see [BCF-API](https://github.com/buildingSMART/BCF-API/tree/release_2_2#429-topic-identifiers). 
When exported to XML this information may be critical to the understanding of topics (e.g. when referenced in the descriptions), so it is an implementation agreement that server-exported BCFs shall always provide the attribute.

However, since the specifications dont't distinguish between server-side BCF and client-side BCF, it was decided to mark the field as optional in the XSD schema.

Clients should:
- display the field and enable searching topcis by its content,
- prevent setting the field when creating a new topic, to avoid any reference in the text by the user,
- prevent changes tp the value when editing an existing topic
- expect that any value provided shall be disregarded upon a server-upload,

**Nodes**

In addition it has the following nodes:

 Element | Optional | Description |
:-----------|:------------|:------------
ReferenceLink | Yes | List of references to the topic, for example, a work request management system or an URI to a model.
Title | No | Title of the topic.
Priority | Yes | Topic priority. The list of possible values are defined in the extension schema.
Index | Yes | Number to maintain the order of the topics. 
Labels | Yes | Tags for grouping Topics. The list of possible values are defined in the extension schema.
CreationDate | No | Date when the topic was created.
CreationAuthor | No | User who created the topic.
ModifiedDate | Yes | Date when the topic was last modified. Exists only when Topic has been modified after creation.
ModifiedAuthor | Yes | User who modified the topic. Exists only when Topic has been modified after creation.
DueDate | Yes | Date until when the topics issue needs to be resolved.
AssignedTo | Yes | The user to whom this topic is assigned to. Recommended to be in email format. The list of possible values are defined in the extension schema.
Description | Yes | Description of the topic.
Stage | Yes | Stage this topic is part of (Predefined list in “extension.xsd”).

### BimSnippet (optional)
BimSnippet is an additional file containing information related to one or multiple topics. For example, it can be an IFC file containing provisions for voids.

 Attribute | Optional | Description |
:-----------|:------------|:------------
SnippetType | No | Type of the Snippet (Predefined list in “extension.xsd”)
IsExternal | Yes | Is the BimSnippet external or within the bcfzip. <br> (Default = false).

 Element | Optional | Description |
:-----------|:------------|:------------
Reference | No | URI to BimSnippet. <br> IsExternal=false  “..\snippetExample.ifc“ (within bcfzip) <br> IsExternal=true  “https://.../snippetExample.ifc“
ReferenceSchema | Yes | URI to BimSnippetSchema (always external)

### DocumentReferences (optional)
DocumentReferences provides a means to associate documents with topics. The references may point to files within the BCF or to external locations.

Attribute | Optional | Description |
:-----------|:------------|:------------
Guid | No | Guid attribute for identifying the reference uniquely
DocumentGuid | No, mutually exclusive with `Url` | Guid of the referenced document. 
Url | No, mutually exclusive with `DocumentGuid` | Url of an external document.
Description | Yes | Human readable description of the document reference

### RelatedTopic (optional)
Relation between topics (Clash -> PfV -> Opening)

Attribute | Optional | Description |
:-----------|:------------|:------------
RelatedTopic/GUID | Yes | List of GUIDs of the referenced topics.


### Comment
The markup file can contain comments related to the topic. Their purpose is to record discussion between different parties related to the topic. Each Comment has a Guid attribute for identifying it uniquely. A comment can reference a viewpoint to support the discussion. At least one of Viewpoint and/or Comment (text) must be provided. In addition, it has the following nodes:

Element | Optional | Description |
:-----------|:------------|:------------
Date | No | Date of the comment
Author |No | Comment author
Comment | Yes, if Viewpoint exists | The comment text, must not be empty if provided 
Viewpoint | Yes, if Comment exists | Back reference to the viewpoint GUID.
ModifiedDate | Yes | The date when comment was modified
ModifiedAuthor | Yes | The author who modified the comment

### Viewpoints
The markup file can contain multiple viewpoints related to one or more comments. A viewpoint has also the Guid attribute for identifying it uniquely. In addition, it has the following nodes:

Element | Optional | Description |
:-----------|:------------|:------------
Viewpoint | Yes | Filename of the viewpoint (.bcfv)
Snapshot | Yes | Filename of the snapshot(.png)
Index | Yes | Parameter for sorting

Viewpoints are immutable, therefore they should never be changed once created. If new comments on a topic require different visualization, new viewpoints should be added.

## Visualization information (.bcfv) file
The visualization information file contains information of components related to the topic, camera settings, and possible markup and clipping information.

### Components

The components node contains a set of Component references. The numeric values in this file are all given in fixed units (meters for length and degrees for angle). Unit conversion is not required, since the values are not relevant to the user. The components node has also the DefaultVisibility attribute which indicates true or false for all components of the viewpoint.

The `Components` element contains the following properties.
* `ViewSetupHints` to describe the visualization settings that were used in the application creating the viewpoint
* `Selection` to list components of interest
* `Visibility` to describe default visibility and exceptions
* `Coloring` to convey coloring options for displaying components

**Composite Components**
When a viewpoint is referring to decomposed components, such as, curtain wall or assemblies, only the parent component should be considered in the components list. If only some parts of decomposed object are visible, then only the child objects should be considered in the components list.

#### Selection
The `Selection` element lists all components that should be either highlighted or selected when displaying a viewpoint.

**Optimization Rules**

BCF is suitable for selecting a few components. A huge list of selected components causes poor performance. All clients should follow this rule:

* If the size of the selected components is huge (approximately 1000 components), alert the user and give him the opportunity to modify the selection.

#### Visibility
The `Visibility` element states the components `DefaultVisibility` and lists all `Exceptions` that apply to specific components. The components in the `Exceptions` have the opposite visibility than `DefaultVisibility.

**Optimization Rules**

BCF is suitable for hiding/showing a few components. A huge list of hidden/shown components causes poor performance. All clients should follow these rules:

* If the list of hidden components is smaller than the list of visible components: set default_visibility to true and put the hidden components in exceptions.
* If the list of visible components is smaller or equals the list of hidden components: set default_visibility to false and put the visible components in exceptions.
* If the size of exceptions is huge (approximately 1000 components), alert the user and give him the opportunity to modify the visibility.

##### ViewSetupHints
This element contains information about the default visibility for elements of certain types (`SpacesVisible`, `SpaceBoundariesVisible` and `OpeningsVisible`). These flags have the following logic when applied to spaces: When a viewpoint has no spaces visible, set the value of `SpacesVisible` to false. If there are any spaces visible in the viewpoint, set the value to the same as `DefaultVisibility`. The DefaultVisibility flag decides whetever to export the hidden or the visible spaces. The logic applied to spaces is also applied to `OpeningsVisible` and `SpaceBoundariesVisible` flags.

##### Applying Visibility
The visibility is applied in following order:
1. Apply the `DefaultVisibility`
2. Apply the `ViewSetupHints`
3. Apply the `Exceptions`

#### Coloring
The `Coloring` element lists colors and a list of associated components that should be displayed with the specified color when displaying a viewpoint. The color is given in ARGB format. Colors are represented as 6 or 8 hexadecimal digits. If 8 digits are present, the first two represent the alpha (transparency) channel. For example, `40E0D0` would be the color <span style="color:#40E0D0;";>Turquoise</span>. [More information about the color format can be found on Wikipedia.](https://en.wikipedia.org/wiki/RGBA_color_space)

**Optimization Rules**

BCF is suitable for coloring a few components. A huge list of components causes poor performance. All clients should follow this rule:

* If the size of colored components is huge (approximately 1000 components), alert the user and give him the opportunity to modify the coloring.

#### Component

A component has the following attributes:

Attribute | Optional | Description |
:-----------|:------------|:------------
IfcGuid | Yes, if AuthoringToolId is Provided | The IfcGuid of the component
OriginatingSystem | Yes | Name of the system in which the component is originated
AuthoringToolId | Yes, if IfcGuid is Provided  | System specific identifier of the component in the originating BIM tool

Note that `IfcGuid` must be provided, if possible. The `AuthoringToolId` can be used as a fallback when an `IfcGuid` is not available.

### Camera

The visualization information file must specify exactly one of either an orthogonal or a perspective camera. 

In either case the projection is centered around the `CameraViewPoint` (i.e. the Left,Bottom point and Right,Top point are centrally symmetric relatively to the ViewCenter).

![Representation of Camera parameters](Graphics/Cameras.png)

**NearPlane** and **FarPlane** clipping values are not considered in the current version, the values that different implementation have adopted, lacking formal requirements, appear to be similar enough to prevent known issues of compatibility.

#### OrthogonalCamera

This element describes a viewpoint using an orthogonal projection. 

It has the following elements:

Element | Optional | Description |
:-----------|:------------|:------------
CameraViewPoint | No | Camera location
CameraDirection | No | Camera direction
CameraUpVector | No | Camera up vector
ViewToWorldScale | No | Vertical scaling from view to world
AspectRatio | No | Proportional relationship between the width and the height of the view (w/h)

#### PerspectiveCamera

This element describes a viewpoint using perspective camera. It has the following elements:

Element | Optional | Description |
:-----------|:------------|:------------
CameraViewPoint | No | Camera location
CameraDirection | No | Camera direction
CameraUpVector | No | Camera up vector
FieldOfView | No | The entire vertical field of view angle of the camera, expressed in degrees
AspectRatio | No | Proportional relationship between the width and the height of the view (w/h)

The `FieldOfView` is currently restricted to a value between 45 and 60 degrees. There may be viewpoints that are not within this range, therefore imports should be expecting any values between 0 and 360 degrees. The limitation will be dropped in the next schema release. 

#### Implementation notes

When reproducing a camera viewpoint on a system that cannot adjust aspect ratio, the actual camera parameters shall be determined to ensure that all the contents of the original **view box** or **view frustum** are displayed, this might result in extra model content visible beyond the original view.

![Adjustment of view on different ratio display](Graphics/RatioAdjustment.png)

Due to incomplete specifications in previous versions, `FieldOfView` and `ViewToWorldScale` were interpreted differently across the various implementers; to mitigate the impact of this differences, when converting legacy BCF files lacking the `AspectRatio` field, the default of `1.0` shall be used and thereafter `FieldOfView` and `ViewToWorldScale` shall be interpreted according to the current specifications.

For any camera, ```CameraDirection``` and ```CameraUpVector``` cannot be zero length vectors or be parallel to each other.

### Lines (optional)
Lines can be used to add markup in 3D. Each line is defined by three dimensional Start Point and End Point. Lines that have the same start and end points are to be considered points and may be displayed accordingly.

### ClippingPlanes (optional)
ClippingPlanes can be used to define a subsection of a building model that is related to the topic. Each clipping plane is defined by Location and Direction. The Direction vector points in the _invisible_ direction meaning the half-space that is clipped. 

### Bitmap (optional)
A list of bitmaps can be used to add more information, for example, text in the visualization. It has the following elements:

Element | Optional | Description |
:-----------|:------------|:------------
Bitmap | No | Format of the bitmap (PNG/JPG)
Reference | No | Name of the bitmap file in the topic folder
Location | No | Location of the center of the bitmap in world coordinates
Normal | No | Normal vector of the bitmap
Up | No | Up vector of the bitmap
Height | No | The height of the bitmap in the model, in meters

## Implementation Agreements
Since BCF 2.0 is compatible with version 1.0, there are some ambiguities in the implementation. The following agreements are written to clarify the implementation.

### One to Many Mapping between Viewpoints and Comments
The schema would allow to have many to many mapping between viewpoints and comments. This is not allowed. A viewpoint can have multiple comments, but a comment can only refer to one viewpoint.

### Status and VerbalStatus to be Phased out
Status and Verbal Status of Comment will be phased out and replaced by TopicStatus and TopicType in Topic. 

When interpreting BCF 1.0 files use the following logic:

- use Status of most recent comment as value of TopicType
- use Verbalstatus of most recent comment as TopicStatus.

When interpreting BCF 2.0 or higher files: VerbalStatus and Status on comment level should all be neglected if TopicStatus and TopicType are present in Topic.

When writing BCF 2.0 or higher files:

- write the current type and status to Topic's TopicType and TopicStatus
- write Status and VerbalStatus at Comment level for backward compatibility.

### .BCFZIP encoding guide
 
Software tool vendors are encouraged to follow the following guidelines to ensure that the .BCFZIP files produced by their software can be correctly exchanged with other tools.

####  Use forward slash as path separator

The forward slash (“/”) should be used as a path separator rather than the backslash (“\”). Windows developers should consult the following MSDN page when attempting to follow this guideline: https://msdn.microsoft.com/en-us/library/mt712573(v=vs.110).aspx


| Correct |  Incorrect |
|---------|------------|
| `742b0df3-9a99-4da3-95ad-171e282f8122/snapshot.png` |  `742b0df3-9a99-4da3-95ad-171e282f8122\snapshot.png` |

#### Create directory (folder) entries in the zip file's central directory

When creating the zip archive make sure to create directory (folder) entries in the .BCFZIP file’s central directory.

**Example of a zip file with directory entries (Correct)**

```
Archive:  correctly_encoded_file_with_directory_entries.bcfzip
 Length      Date    Time    Name
---------  ---------- -----   ----
     217  2015-02-18 09:12   bcf.version
       0  2015-02-18 09:12   bff0f7ed-6b0d-4aad-ab28-401cc1db7f6e/
    1782  2015-02-18 09:12   bff0f7ed-6b0d-4aad-ab28-401cc1db7f6e/markup.bcf
     565  2015-02-18 09:12   bff0f7ed-6b0d-4aad-ab28-401cc1db7f6e/viewpoint.bcfv
  181641  2015-02-18 09:12   bff0f7ed-6b0d-4aad-ab28-401cc1db7f6e/snapshot.png
---------                     -------
  184205                     5 files
```

**Example of a zip file without directory entries (Incorrect)**
```
Archive:  incorrect_file_without_directory_entries.bcfzip
 Length      Date    Time    Name
---------  ---------- -----   ----
   35525  2015-02-25 09:28   742b0df3-9a99-4da3-95ad-171e282f8122/snapshot.png
     681  2015-02-25 09:28   742b0df3-9a99-4da3-95ad-171e282f8122/viewpoint.bcfv
    1057  2015-02-25 09:28   742b0df3-9a99-4da3-95ad-171e282f8122/markup.bcf
     119  2015-02-25 09:28   bcf.version
---------                     -------
   37382                     4 files
```


#### How to verify

##### On windows 7 using 7zip and the windows command prompt

1. Download and install 7zip from http://www.7-zip.org/download.html
2. On the start menu type ‘cmd’ and select cmd.exe
3. Execute the following:

`“c:\Program Files\7-Zip\7z.exe"  l $PATH_TO_BCFZIP`

**Example output**
```
7-Zip [64] 9.20  Copyright (c) 1999-2010 Igor Pavlov  2010-11-18

 Listing archive: E:\BIM\bcf files\bulkExport_1topic.bcfzip

 --
 Path = #### Path to the bcfzip file
 Type = zip
 Physical Size = 183599

    Date      Time    Attr         Size   Compressed  Name
 ------------------- ----- ------------ ------------  ------------------------
 2015-02-18 09:12:40 .....          217          161  bcf.version
 2015-02-18 09:12:40 D....            0            0  bff0f7ed-6b0d-4aad-ab28-401cc1db7f6e
 2015-02-18 09:12:40 .....         1782          632  bff0f7ed-6b0d-4aad-ab28-401cc1db7f6e\markup.bcf
 2015-02-18 09:12:40 .....          565          336  bff0f7ed-6b0d-4aad-ab28-401cc1db7f6e\viewpoint.bcfv
 2015-02-18 09:12:40 .....       181641       181678  bff0f7ed-6b0d-4aad-ab28-401cc1db7f6e\snapshot.png
 ------------------- ----- ------------ ------------  ------------------------
                                 184205       182807  4 files, 1 folders

```

##### On Unix or Linux shell

1. Start your favourite shell and execute the following:

``` $ unzip -l $PATH_TO_BCFZIP ```

**Example output**
```
Archive:  /mnt/engineering/BIM/bcf files/bulkExport_1topic.bcfzip
  Length      Date    Time    Name
---------  ---------- -----   ----
      217  2015-02-18 09:12   bcf.version
        0  2015-02-18 09:12   bff0f7ed-6b0d-4aad-ab28-401cc1db7f6e/
     1782  2015-02-18 09:12   bff0f7ed-6b0d-4aad-ab28-401cc1db7f6e/markup.bcf
      565  2015-02-18 09:12   bff0f7ed-6b0d-4aad-ab28-401cc1db7f6e/viewpoint.bcfv
   181641  2015-02-18 09:12   bff0f7ed-6b0d-4aad-ab28-401cc1db7f6e/snapshot.png
---------                     -------
   184205                     5 files
```
### Additional Document Properties

If any Xml file in an imported BCF container contains additional or unknown properties, BCF clients shall ignore them and not produce errors. This is to allow BCF implementations the freedom to add additional functionality. Client implementations are not required to preserve these properties.
