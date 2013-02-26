## Introduction



## Goal



## Interfaces

Extensions are optional features which do not need to be provided by the
Implementing Library but which may provide useful functionality or insights.
Calling Libraries should not rely on any extension being present, but can use
the relevant interfaces to test for functionality. These extensions primarily
exist to show how extending this standard can be done by interested developers.


### Namespaces

Namespaces can be used to separate the storage of different systems in the
cache. This allows different sections to be cleared on an individual level,
while also preventing overlapping keys.

Supporting namespaces is out of the scope of this standard, but can easily be
accomplished by the Implementing Library as part of the Cache\Pool. Different
Cache\Pool objects can be assigned namespaces and then get injected into their
respective Calling Libraries, and those libraries will not need to treat them
any differently.


### Stacks

Stacks are a special kind of grouping system that allow cache items to be
nested, similar to how folders are nested in filesystems. Stacks work by adding
a special character to Keys, the slash, which tells the Implementing Library
where the nesting points are. If the first character of the key is not a slash,
Stacks behaves exactly like the standard Cache interfaces.

> An example key may look like "/Users/Bob/Friends", "/Users/Bob/Friends/Active"
or just "/Users/Bob". The special thing about Stacks is that clearing out
"/Users/Bob" also clears out "/Users/Bob/Friends" and "/Users/Bob/Friends/Active".


#### StackablePool

```php
<?php
namespace PSR\Cache\Extensions;

/**
 * Cache\Extensions\StackablePool extends Cache\Pool to provide stacking support.
 *
 * The Cache\Extensions\StackablePool interface adds support for returning
 * Cache\Extensions\StackableItem objects. This works primarily by defining the
 * Key to use a slash as a delimiter, similar to a filesystem, to nest keys.
 * When a StackableItem is cleared it also clears the items nested beneath it.
 */
interface StackablePool extends \PSR\Cache\Pool
{

}
```


#### StackableItem

```php
<?php
namespace PSR\Cache\Extensions;

/**
 * Cache\Extensions\StackableItem extends Cache\Item to provide stacking support.
 *
 * The Cache\Extensions\StackableItem interface adds support for stacking to the
 * base Cache\Item interface. When a StackableItem is cleared it also clears the
 * items nested beneath it.
 */
interface StackableItem extends \PSR\Cache\Item
{

}

```


### Tags

Tagging interfaces are provided for completeness, but developers should note the
difficulty in providing a consistent high performance tagging solution.


#### Cache\Extensions\TaggablePool

```php
<?php
namespace PSR\Cache\Extensions;

/**
 * Cache\Extensions\TaggablePool extends Cache\Pool to provide tagging support.
 *
 * The Cache\Extensions\TaggablePool interface adds support for returning
 * Cache\Extensions\TaggableItem objects, as well as clearing the pool of tagged
 * Items.
 */
interface TaggablePool extends \PSR\Cache\Pool
{
    /**
     * Clears the cache of all items with the specified tag.
     *
     * @param string $tag
     * @return bool
     */
    function clearByTag($tag);
}
```


#### Cache\Extensions\TaggableItem

```php
<?php
namespace PSR\Cache\Extensions;

/**
 * Cache\Extensions\TaggableItem extends Cache\Item to provide tagging support.
 *
 * The Cache\Extensions\TaggableItem interface adds support for tagging to the
 * base Cache\Item interface. Items can be added to multiple categories (called
 * tags) that can be used for group invalidation.
 */
interface TaggableItem extends \PSR\Cache\Item
{
    /**
     * Sets the tags for the current item.
     *
     * Accepts an array of strings for the item to be tagged with. The tags
     * passed should overwrite any existing tags, and passing an empty array
     * will cause all tags to be removed. Changes to an Item's tags are not
     * guaranteed to persist unless the "set" function is called.
     *
     * @param array $tags
     * @return void
     */
    function setTags(array $tags = array());
}


```
