---
title: Meta Box Geolocation
---

## Overview

The Meta Box Geolocation is helpful if you have multiple custom fields for location such as address, zipcode, city, state and country. Letting users manually fill in these fields might be a boring time-consuming job. This extension helps you fill in these information quickly by using Geocoding API from Google. So that you just need to enter an address, it will autosuggest a complete address and fill all other fields.

Take a look at the screenshot:

![auto suggest geolocation data](https://i1.wp.com/metabox.io/wp-content/uploads/2016/03/meta-box-geolocation.gif)

For more information, please see the [extension page](https://metabox.io/plugins/meta-box-geolocation/).

{% include installation.html %}

Make sure you know how to [create meta boxes](/creating-meta-boxes/) and [fields](/field-settings/) before continuing!

## Enabling geolocation

### Google Maps API

Before enabling Geolocation, make sure you create a project for Google Maps in the [Google Cloud Platform Console](https://console.cloud.google.com/google/maps-apis/overview). And don't forget to enable the following APIs:

- Google Maps API
- Geocoding API
- Places API

And then create an API key for Google Maps. See [here](https://developers.google.com/maps/documentation/javascript/get-api-key) for instruction. You'll need this key to perform any call to Google Maps platform.

### Enable geolocation support for a meta box.

To enable geolocation support for a meta box, add this line to your Meta Box (not field) settings:

```php
'geo' => array(
    'api_key' => 'YOUR API KEY',
)
```

If you're using [Meta Box Builder](https://metabox.io/plugins/meta-box-builder/), then please go to tab *Settings* and add a custom attribute for the geolocation as follows:

![adding google maps api key in builder](https://i.imgur.com/sk6NzTj.png)

You can also add options to Geolocation. Which can contains these properties:

- `types`: array of geocode types:
    - `geocode`: only geocoding results, no business results.
    - `address`: only geocoding results with a precise address.
    - `establishment`: business results.
    - `(cities)`: only results that match either `locality` or `administrative_area3`.
    - `(regions)`: any result matching the following types:
        - `locality`
        - `sublocality`
        - `postal_code`
        - `country`
        - `administrative_area1`
        - `administrative_area2`
- `componentRestrictions`: restrict results to a specific country, must be ISO 3166-1 Alpha-2 compatible country code. You can find code information at [Wikipedia: List of ISO 3166 country codes](https://en.wikipedia.org/wiki/List_of_ISO_3166_country_codes).

Examples:

```php
// Restrict the result in Australia only
'geo' => array(
    'api_key' => 'YOUR API KEY',
    'componentRestrictions' => array(
        'country' => 'au'
    )
)

// Return only business results
'geo' => array(
    'api_key' => 'YOUR API KEY',
    'types' => array( 'establishment' )
),

// Return only cities and business results
'geo' => array(
    'api_key' => 'YOUR API KEY',
    'types' => array( '(cities)', 'establishment' )
),
```

## Adding autocomplete field

To add an auto complete field. Simply add a `text` field and set it id starts with `address`. Like `address` or `address_something`... The plugin will treat that field as auto complete address field.

You can add auto complete to `textarea`, it works but will generate warning messages.

## Populating field data

### Address Components
When you selected an address from Auto-Complete field. It returns an array of *Address Component Types*. Which you can use it to populate to other fields. The following types are supported by Google:

Type|Description
---|---
`street_address`|indicates a precise street address.
`route`|indicates a named route (such as "US 101").
`intersection`|indicates a major intersection, usually of two major roads.
`political`|indicates a political entity. Usually, this type indicates a polygon of some civil administration.
`country`|indicates the national political entity and is typically the highest order type returned by the Geocoder.
`administrative_area_level_1`|indicates a first-order civil entity below the country level. Within the United States, these administrative levels are states. Not all nations exhibit these administrative levels.
`administrative_area_level_2`|indicates a second-order civil entity below the country level. Within the United States, these administrative levels are counties. Not all nations exhibit these administrative levels.
`administrative_area_level_3`|indicates a third-order civil entity below the country level. This type indicates a minor civil division. Not all nations exhibit these administrative levels.
`administrative_area_level_4`|indicates a fourth-order civil entity below the country level. This type indicates a minor civil division. Not all nations exhibit these administrative levels.
`administrative_area_level_5`|indicates a fifth-order civil entity below the country level. This type indicates a minor civil division. Not all nations exhibit these administrative levels.
`colloquial_area`|indicates a commonly-used alternative name for the entity.
`locality`|indicates an incorporated city or town political entity.
`sublocality`|indicates a first-order civil entity below a locality. For some locations may receive one of the additional types: sublocality_level_1 through to sublocality_level_5. Each `sublocality`|level is a civil entity. Larger numbers indicate a smaller geographic area.
`neighborhood`|indicates a named neighborhood.
`premise`|indicates a named location, usually a building or collection of buildings with a common name
`subpremise`|indicates the first-order entity below a named location, usually a singular building within a collection of buildings with a common name.
`postal_code`|indicates a postal code as used to address postal mail within the country.
`natural_feature`|indicates a prominent natural feature.
`airport`|indicates an airport.
`park`|indicates a named park.
`post_box`|indicates a specific postal box.
`street_number`|indicates the precise street number.
`floor`|indicates the floor of a building address.
`room`|indicates the room of a building address.
`lat`|indicates latitude of the result
`lng`|indicates longitude of the result
`geometry`|same as `latitude,longitude`
`formatted_address`|returns human readable complete address
`id`|unique id of current location
`url`|url of current location

For more information about address components visit: [Google Address Component Types](https://developers.google.com/maps/documentation/geocoding/intro#Types)

### Auto Populate

To auto populate data to the field. Just set that field's ID same as one of Address Component Type above. For example, you create a `number` field with ID `postal_code`. The postal code type will auto populate to that field if exists.

**Long name and Short Name**

Let's say we have a country named 'Australia'. The long name is 'Australia' and short name is 'AU'. And so on, if we have a state named 'Queensland', so 'Queensland' is the long name and 'QLD' is the short name.

By default, Meta Box Geolocation will populate the long name of the field. But you can use the short name by adding `_short` at the end of field id. For example: `administrative_area_level_1_short`, `country_short`

### Binding Template

The power of Meta Box Geolocation is you can bind anything into a field. Let's say we have a field with id `dummy_field`, this field id is not in *Address Component Types* list so by default the plugin won't auto populate data to it. Now we'll bind `administrative_area_level_1` to that field. Just add

```php
'binding' => 'administrative_area_level_1'
```

Example Result: `Queensland`

You can tell that field to use short version by prepending `short:` keyword

```php
'binding' => 'short:administrative_area_level_1'
```

Example Result: `QLD`

You can also merge two fields, add any character you want to bind to that field. Like so:

```php
'binding' => 'short:administrative_area_level_1 + " " + country'
```

Example Result: `QLD Australia`

## Multiple autocomplete addresses

In case you have multiple "group" of address fields, such as address details for head office and address details for a company branch. In each group, there are an autocomplete address field. And it will autopopulate only fields in the group, e.g. the address for head office only populates the fields in the head office group, not in the company branch.

In this case, please set another attribute `'address_field' => 'address_id'` for *each* field in the group. See an example code below:

```php
/**
 * Head office
 */
 
// Address
array(
    'id' => 'address_ho',
    'type' => 'text',
    'name' => 'Address - Head Office',
),
// City
array(
    'id' => 'city_ho',
    'type' => 'text',
    'name' => 'City - Head Office',
    'binding' => 'locality',
    'address_field' => 'address_ho', // THIS
),

/**
 * A company branche
 */
 
// Address
array(
    'id' => 'address_br',
    'type' => 'text',
    'name' => 'Address - Branch',
),
// City
array(
    'id' => 'city_br',
    'type' => 'text',
    'name' => 'City - Branch',
    'binding' => 'locality',
    'address_field' => 'address_br', // THIS
),
```

## Google API Key in old versions

Prior version 1.2.0, you need to use a filter to `gmap_api_params` like so:

```php
add_filter( 'gmap_api_params', function( $params ) {
    $params['key'] = 'YOUR API KEY';
    return $params;
});
```

Also notes that since version 1.2.1, if you use Geolocation with `map` field, you need to enter the Google API key only once, either for the Geolocation extension or for the `map`. No need to enter the same key for both of them.
