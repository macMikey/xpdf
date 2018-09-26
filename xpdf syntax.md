#XPDFViewer External For LiveCode

Developed by Tuviah Snyder and [Monte Goulding](monte@goulding.ws) for [ResearchWare, Inc.](http://researchware.com)

##Documentation

###API

Errors as documented will be returned in the result of the command or function. In all cases if the incorrect number of parameters were used a script execution error will be thrown with the error `XPDF_ERROR: incorrect number of parameters`.

####XPDFViewer_Open <viewer name>, <stack windowId>

Create a named PDF Viewer associated with a specific stack window.

	XPDFViewer_Open "Document1", the windowID of this stack

Errors:
 * XPDF_ERROR: PDF viewer with that name exists

####XPDFViewer_GoToPage <viewer name>, {"last" | "first" | "next" | "prev" | <page number>}

Navigate to a page using either one of the four navigation constants or a specific page number.

	XPDFViewer_GoToPage "Document1", "next"

Errors:
 * XPDF_ERROR: PDF viewer not found

####XPDFViewer_ChooseMouseMode <viewer name>, {"selection" | "browse" | "image" }

Choose a mouse mode. The viewer defaults to browse mode. Choose selection to select text.

	XPDFViewer_ChooseMouseMode "Document1", "selection"

Errors:
 * XPDF_ERROR: PDF viewer not found

####XPDFViewer_Zoom <viewer name>, {"width" | "fit" | "actual" | "in" | "out" | <percent>}

Zoom the page using either one of the five zoom constants or a specified percentage.

	XPDFViewer_Zoom "Document1", 200

Errors:
 * XPDF_ERROR: PDF viewer not found

####XPDFViewer_Close <viewer name>

Close a named PDF viewer

	XPDFViewer_Close "Document1"

Errors:
 * XPDF_ERROR: PDF viewer not found

####XPDFViewer_Set <viewer name>, <property>, <value>

Set a property of the PDF Viewer. See the [property reference](#markdown-header-property-reference) for detail of the specific properties.

	XPDFViewer_Set "Document1", "rect", (100, 100, 500, 600)

Errors:
 * XPDF_ERROR: PDF viewer not found
 * XPDF_ERROR: can't set property
 * further errors described in the [property reference](#markdown-header-property-reference)

####XPDFViewer_Get(<viewer name>, <property>)

Get the value of a property from a PDF Viewer. See the [property reference](#markdown-header-property-reference) for detail of the specific properties.

	put XPDFViewer_Get("Document1", "rect") into thePDFRect

Errors:
 * XPDF_ERROR: PDF viewer not found
 * XPDF_ERROR: can't get property

####XPDFViewer_Text(<viewer name>, <page number>)

Return the text of a page in ANSI. This function should be considered deprecated in favor of `XPDFViewer_Unicode`

	put XPDFViewer_Text("Document1",3) into fld "page text"

Errors:
 * XPDF_ERROR: PDF viewer not found

###XPDFViewer_Unicode <viewer name>, <page number>, <variable name to set>

This command sets the value of a named variable to the UTF16 encoded text of a specific page.

	local thePageText
	XPDFViewer_Unicode "Document1", 3, "thePageText"
	// LiveCode 6
	set the unicodeText of fld "page text" to thePageText
	// LiveCode 7+
	set the text of fld "page text" to thePageText

Errors:
 * XPDF_ERROR: PDF viewer not found

####XPDFViewer_CharacterCount(<viewer name>, <page number>)

Return the number of characters in a specified page.

	put XPDFViewer_CharacterCount("Document1", 3) into theNumberOfChars
	

Errors:
 * XPDF_ERROR: PDF viewer not found

###XPDFViewer_Image <viewer name>, <page number>, <variable name to set>

This command sets the value of a named variable to the imageData of the rendered page. The page is rendered at the current viewer scale. LiveCode imageData does not contain any width or height information so you must set the width and height of the image correctly *before* setting the imageData. The command returns the size (width, height) of the image. As the image is drawn at the current scale of the PDF view the width/height returned will not match the width/height calculated from `XPDFViewer_Get("Document1","width")` and `XPDFViewer_Get("Document1","height")` on Mac retina and high DPI windows systems.

    local tImageData
    XPDFViewer_Image "Document1", 1, "tImageData"
    put the result into tSize
    set the width of image "page" to item 1 of tSize
    set the height of image "page" to item 2 of tSize
    set the imageData of image "page" to tImageData

Errors:
* XPDF_ERROR: PDF viewer not found

###Text Selection

####XPDFViewer_GetSelectionRect(<viewer name>[, <page number>])

Get the rect of the portion of the selection that appears on the currently loaded page. If there is no selection on the current page the function returns 0,0,0,0. Coordinates are in stack coordinates. Adjust the returned rect by the topLeft of the RECT property (viewport) to get coordinates relative to the PDF page.

An optional page number parameter may be used to get the rect of the selection or portion of selection that appears on the given page.

    put XPDFViewer_GetSelectionRect("Document1") into theSelectionRect

Errors:
* XPDF_ERROR: PDF viewer not found

####XPDFViewer_GetSelectionUnicode <viewer name>, <variable name to set>

This command sets the value of a named variable to the UTF16 encoded text of the current selection.

    local theSelectedText
    XPDFViewer_GetSelectionUnicode "Document1", "theSelectedText"
    // LiveCode 6
    set the unicodeText of fld "selected text" to theSelectedText
    // LiveCode 7+
    set the text of fld "selected text" to theSelectedText

Errors:
* XPDF_ERROR: PDF viewer not found

####XPDFViewer_GetSelectionText(<viewer name>)

Return the selected text in ANSI. This function should be considered deprecated in favor of `XPDFViewer_GetSelectionUnicode`

    put XPDFViewer_GetSelectionText("Document1") into fld "selected text"

Errors:
* XPDF_ERROR: PDF viewer not found

####XPDFViewer_GetSelectionRange(<viewer name>)

Get the current selection range from a PDF Viewer. The function returns the range as <start page number>, <start char on page>, <end page number>, <end char on page>

    put XPDFViewer_GetSelectionRange("Document1") into theCurrentSelection

And insertion point is returned as <start page number>,<char insertion point is after>,<end page number - same as start>,<char insertion point is after - 1>

If item 1 = item 3 AND item 4 < item 2 then it is an insertion point position.

The function will return empty if there is no selection.

Errors:
* XPDF_ERROR: PDF viewer not found

####XPDFViewer_Select <viewer name>, <start page number>, <start char on page>, <end page number>, <end char on page>

Set the current selection of the PDF Viewer to a specific range.

    XPDFViewer_Select "Document1", 1, 300, 3, 20

Clear the current selection of the PDF Viewer.

    XPDFViewer_Select "Document1",""

Errors:
* XPDF_ERROR: PDF viewer not found

###Image Selection

####XPDFViewer_GetImageSelectionRect(<viewer name>)

Get the rect of image selection. If there is no image selection the function returns 0,0,0,0. Coordinates are in stack coordinates. Adjust the returned rect by the topLeft of the RECT property (viewport) to get coordinates relative to the PDF page.

    put XPDFViewer_GetImageSelectionRect("Document1") into theSelectionRect

Errors:
* XPDF_ERROR: PDF viewer not found

####XPDFViewer_GetSelectionImage <viewer name>, <variable name to set>

This command sets the value of a named variable to the imageData of the current image selection. The selection is rendered at the current viewer scale. LiveCode imageData does not contain any width or height information so you must set the width and height of the image correctly *before* setting the imageData. The command returns the size (width, height) of the image. As the image is drawn at the current scale of the PDF view the width/height returned will not match the width/height calculated from `XPDFViewer_GetImageSelectionRect` on Mac retina and high DPI windows systems.

    local tImageData
    XPDFViewer_GetSelectionImage "Document1", "tImageData"
    put the result into tSize
    set the width of image "selection" to item 1 of tSize
    set the height of image "selection" to item 2 of tSize
    set the imageData of image "selection" to tImageData

Errors:
* XPDF_ERROR: PDF viewer not found

####XPDFViewer_GetImageSelectionRange(<viewer name>)

Get the current image selection range from a PDF Viewer. The function returns the range as <page number>, <left>, <top>, <right>, <bottom>. The coordinates are real numbers in pdf coordinates rather than integers stack coordinates. 

    put XPDFViewer_GetImageSelectionRange("Document1") into theCurrentSelection

The function will return empty if there is no selection.

Errors:
* XPDF_ERROR: PDF viewer not found

####XPDFViewer_SelectImage <viewer name>, <page number>, <left>, <top>, <right>, <bottom>

Set the current image selection of the PDF Viewer to a specific range. The coordinates are real numbers in pdf coordinates rather than integers stack coordinates. 

    XPDFViewer_SelectImage "Document1", 1, 10.5, 100.2, 200, 130.4

Clear the current selection of the PDF Viewer. 

    XPDFViewer_SelectImage "Document1",""

Errors:
* XPDF_ERROR: PDF viewer not found

###Messages

####XPDF_ScrollTopOfPage

Sent when scrolling past the top of page

####XPDF_SelectTopOfPage

Sent when selecting past the top of page

####XPDF_ScrollBottomOfPage

Sent when scrolling past the bottom of page

####XPDF_SelectBottomOfPage

Sent when selecting past the bottom of page

####XPDF_SelectionChanged (<start page number>, <start char on page>, <end page number>, <end char on page>)

Sent when the selected text is changed. If there is no selection then the parameter is empty.

####XPDF_ImageSelectionChanged (<page number>, <left>, <top>, <right>, <bottom>)

Sent when the selected image is changed. If there is no selection then the parameter is empty. The rect is in PDF coordinates rather than stack coordinates.

####XPDF_ScrollbarDragged (<hscroll>, <vscroll>)

Sent when the user scrolls the view.

####XPDF_LinkClicked <uri or file path>

Sent when the user clicks on a link

###Property Reference

####RECT

The rect of the PDF Viewer relative to the top left of the stack window.

Errors:
 * XPDF_ERROR: invalid rect

####FILENAME

The full file path to the PDF file being viewed

Errors:
 * XPDF_ERROR: could not open file
 * XPDF_ERROR: password required or incorrect password

####VISIBLE

The visibility of the PDF Viewer

####PAGENUMBER

The currently displayed page of the PDF Viewer

####FORMATTEDWIDTH (read only)

The width in pixels of the PDF Page with no scaling applied.

####FORMATTEDHEIGHT (read only)

The height in pixels of the PDF Page with no scaling applied.

####WIDTH (read only)

The width in pixels of the PDF page with scaling applied.

####HEIGHT (read only)

The height in pixels of the PDF page with no scaling applied.

####PAGECOUNT (read only)

The total number of pages in the PDF document.

####TOTALCHARACTERCOUNT (read only)

The total number of characters in the PDF document.

####CONTINUOUSSCROLLMODE

The continuous scroll mode of the PDF document.

####SCROLL

The current scroll of the displayed page as <horizontal scroll>,<vertical scroll>.

Errors:
 * XPDF_ERROR: invalid scroll

####PAGEGRAVITY

The gravity of the page when the view is larger than the scaled page in either width or height.
Currently supported page gravities are:

 * topLeft (default) - top left of page sticks to the top left of the view
 * center - the page is centered in the view

####HSCROLLBAR

Boolean property to show or hide the horizontal scrollbar

####VSCROLLBAR

Boolean property to show or hide the vertical scrollbar

