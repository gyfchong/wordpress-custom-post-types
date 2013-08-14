# WordPress Custom Post Types

> A PHP class to help register and maintain WordPress custom post types. It also comes with some rad built-in properties and methods that can be used in templates to maintain clean code and modular development.

For more information about registering post types, including a full list of options, visit the [WordPress Codex](http://codex.wordpress.org/Function_Reference/register_post_type).

This class works well with the [WordPress Custom Taxonomy class](https://github.com/beaucharman/wordpress-custom-taxonomy). They were made for each other <3.

### Declaring New Post Types

Include [`custom-post-type.php`](https://github.com/beaucharman/wordpress-custom-post-types/blob/master/custom-post-type.php) in your `functions.php` file, with something like `require_once(get_template_directory() . '/includes/custom-post-types.php');`.

Declare the various argument arrays (within `funtions.php` or another initiating include file) to setup the new post type as needed (`$name` is required):

```PHP
// required
$name = '';

// optional
$labels = array(
  'label_singular' => '',
  'label_plural'   => '',
  'menu_label'     => ''
);

$options = array(
  'description'    => '',
  'public'         => true,
  'menu_position'  => 20,
  'menu_icon'      => null,
  'hierarchical'   => false,
  'supports'       => array( '' ),
  'taxonomies'     => array( '' ),
  'has_archive'    => true,
  'rewrite'        => true
);

$help = array(
  array(
    'message'      => ''
  ),
  array(
    'context'      => 'edit',
    'message'      => ''
  )
);
```
Then create a variable (for future reference, but is not required) from an instance of the `LT3_Custom_Post_Type` class:

```PHP
$PostType = new LT3_Custom_Post_Type( $name, $labels, $options, $help );
```

### Usage

The custom post type class creates a handfull of useful properties and methods that can be accessed through the post type's instance variable and can be used in template and admin files.

#### Properties

**`$PostType->name`**

The post type slug.

**`$PostType->lables`**

An array of the singular, plural and menu lables.

#### Methods

**`$PostType->archive_uri()`**

Gets the URI to the post type's archive page.

**`$PostType->get()`**

Get all entries assigned to this post type. Accepts an array of arguments, and a boolean value to retrieve just a single value (true, useful to use along side `'include' => $single_id`) or an array of results (false).

For example:

```PHP
$post_types = $PostType->get();
```

**Note:** A declaration of `global $PostType;` might be required on some template files.

See the [Get Posts => Default Usage](http://codex.wordpress.org/Template_Tags/get_posts#Default_Usage) codex reference for the list of possible arguments, and the [Get Pages => Return](http://codex.wordpress.org/Function_Reference/get_pages#Return) codex reference for the list of return values.

####Working with Custom Taxonomies

To get posts within (or maybe even not within) terms of particular taxonomies, you can use the [Tax Query](https://codex.wordpress.org/Class_Reference/WP_Query#Taxonomy_Parameters) option within your get function `$args`. For example:

```PHP
$args = array(
  'tax_query' => array(
    array(
      'taxonomy'         => 'foo_category',
      'field'            => 'slug',
      'terms'            => array( 'bar', 'baz' ),
      'include_children' => true,
      'operator'         => 'IN'
    )
  )
);
$post_types = $PostType->get( $args );
```

### Flush Rewrites

If there are issues with permalinks and the new post types, even after flushing them in the administrator area (Settings > Permalinks > Save Changes), use the following function to flush permalink rewrites for new custom post types and taxonomies.

```PHP
add_action( 'init', 'lt3_post_type_and_taxonomy_flush_rewrites' );
function lt3_post_type_and_taxonomy_flush_rewrites()
{
  global $wp_rewrite;
  $wp_rewrite->flush_rules();
}
```
