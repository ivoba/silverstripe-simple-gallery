# Simple Gallery

A simple photogallery management module.

## Introduction

With this module you will be able to extend each DataObjects, including Pages, in order associate to it a photogallery. If a Page needs more
than a single photogallery, than it's possible to create a Gallery Dataobject, extend it with SimpleGallery, than assign it in a has_many relationship
to the page. See example below.

## Requirements

 * SilverStripe ^4.0 (version master/4+)
 * SilverStripe ^3.1 (version 1.+ / 3.+)
 * colymba/gridfield-bulk-editing-tools ^3.0.* (version master/4+)
 * colymba/gridfield-bulk-editing-tools ^2.1.* (version 1.+ / 3.+) 
 * undefinedoffset/sortablegridfield ^2.0.* (version master/4+)
 * undefinedoffset/sortablegridfield ^0.* (version 1.+ / 3.+)

## Installation

Install the module through [composer](http://getcomposer.org):

    composer require g4b0/simple-gallery
    composer update

### Single gallery

To have a single gallery per page extend the desired page type through the following yaml:

```YAML
Page:
  extensions:
    - g4b0\SimpleGallery\SimpleGalleryExtension
```

#### Template

A really basic template is given, to use it just put this code into your Page template:

```HTML
<% include simplegallery %>
```

For a nice visualization into the default SS template add this CSS code:

```CSS
.simplegallery img {width: 30%; float: left; margin: 10px;}
```

You have to write your own .ss files for more advanced features. Just overwrite template/simplegallery.ss into your theme and loop over 
$SortedImages writing your HTML code:

```HTML
<div class="simplegallery">
<% loop SortedImages %>
	<% if $CustomLink %>
	 <a href="$CustomLink">$Image</a>
	<% else %>
	 $Image
	<% end_if %>
<% end_loop %>
</div>
```

You can access the following image variable:

$CustomLink
$Title
$Text
$Disabled
$SortOrder

### Multiple gallery

If you prefer to have multiple sortable gallery in a specific page type simply add an has_many relationship
like the following example:

```php
class Portfolio extends Page {

	private static $has_many = array(
			'Galleries' => 'SimpleGallery'
	);

	public function getCMSFields() {
		$fields = parent::getCMSFields();

		$gridFieldConfig = GridFieldConfig_RelationEditor::create();
		$gridFieldConfig->addComponent(new GridFieldSortableRows('SortOrder'));
		$field = new GridField(
						'Galleries', 'Galleries', $this->SortedGalleries(), $gridFieldConfig
		);
		$fields->addFieldToTab('Root.Galleries', $field);

		return $fields;
	}

	public function SortedGalleries() {
		return $this->Galleries()->sort('SortOrder');
	}

}
```

#### Template

Just like simplegallery, a very basic template is given, to use it just put this code into your Page template:

```HTML
<% include simplegalleries %>
```

For a nice visualization into the default SS template add this CSS code:

```CSS
.simplegallery {clear: both;}
.simplegallery img {width: 30%; float: left; margin: 10px;}
```

You have to write your own .ss files for more advanced features. Just overwrite template/simplegalleries.ss into your theme and loop 
over $SortedGalleries, and then over $SortedImages

```HTML
<% loop $SortedGalleries %>
	<div class="simplegallery">
		<article>
			<h3>$Name</h3>
			$Description
			<div>
			<% loop SortedImages %>
				<% if $CustomLink %>
				 <a href="$CustomLink">$Image</a>
				<% else %>
				 $Image
				<% end_if %>
			<% end_loop %>
			</div>
		</article>
	</div>
<% end_loop %>
```

## Config

Editing _config/simplegallery.yml it's possible to modify gallery behaviour:

	* gallery_name: The gallery name that appear in backend tabs (default: Gallery)
  * folder_path: path where the uploaded images are stored (default: simplegallery)