# wooCommerce-DisplayProducts-php-loop
wooCommerce Display Product PHP Loop
# wooCommerce Development 
<br /> https://woocommerce.com/document/introduction-to-hooks-actions-and-filters/ 
<br /> https://woocommerce.github.io/code-reference/index.html
<br /> https://woocommerce.github.io/code-reference/hooks/hooks.html
<br /> https://woocommerce.github.io/woocommerce-rest-api-docs/#introduction

Creating a WooCommerce product display layout for your WordPress theme
LAST UPDATED: AUGUST 7TH, 2015 BY: GAVICKPRO TEAM PUBLISHED IN WORDPRESS
In my previous WordPress development article, we discussed using WP_Query to pull WooCommerce product data that we can then display in a simple layout. Now let’s look at manipulating this layout to provide additional details that put the data we’ve pulled to good use.

wp query

The Basic Code
Our basis for the examples discussed here will be the core code we created in my previous article:

```PHP
<?php
 $params = array('posts_per_page' => 5, 'post_type' => 'product');
 $wc_query = new WP_Query($params);

?>
```
```PHP
<?php if ($wc_query->have_posts()) : ?>
<?php while ($wc_query->have_posts()) :
                $wc_query->the_post(); ?>
<?php the_title(); ?>
<?php endwhile; ?>
<?php wp_reset_postdata(); ?>
<?php else:  ?>
<p>
     <?php _e( 'No Products'); ?>
</p>
<?php endif; ?>

```

This code will pull the product data we need; now we can start putting it to use.

Standard Functions for WooCommerce Product Displays
Since WooCommerce products are simply posts with a product type attached, we can take advantage of the standard WordPress post functions to create our displays, such as:

```PHP 

the_title()           // – Displays the name of the product.
the_excerpt()         // – Displays a brief description of the product.
the_content()         // – Displays the full description of the product.
the_permalink()       //  – Displays the URL of the product.
the_ID()              // – Displays the product’s ID.
the_post_thumbnail()  // – Displays the product image.

```

If we create some example code that uses these basic functions to make a layout, it’ll look something like this:

```PHP
<?php
$params = array('posts_per_page' => 5, 'post_type' => 'product');
$wc_query = new WP_Query($params);

?>
```
```PHP
<ul>
     <?php if ($wc_query->have_posts()) : ?>
     <?php while ($wc_query->have_posts()) :
                $wc_query->the_post(); ?>
     <li>
          <h3>
               <a href="<?php the_permalink(); ?>">
               <?php the_title(); ?>
               </a>
          </h3>
          <?php the_post_thumbnail(); ?>
          <?php the_excerpt(); ?>
     </li>
     <?php endwhile; ?>
     <?php wp_reset_postdata(); ?>
     <?php else:  ?>
     <li>
          <?php _e( 'No Products' ); ?>
     </li>
     <?php endif; ?>
</ul>

```

These basic functions are great for creating a simple layout, but WooCommerce offers a number of other elements that are worth taking into consideration when crafting your product list. Let’s take a look at some of them, along with some example code that you can insert into the product-display code loop:

Grabbing WooCommerce product data
Many of the examples we’re going to look at will need to use additional data and methods that are exclusively available in WC_Product class objects; to create an object of this class for a particular product we have to call the following code in our product-display loop:

```PHP
$product = new WC_Product(get_the_ID()); </code>
</pre>
<p>
     Alternatively, we can use the <b>get_product</b> function:
</p>
<pre>
<code> $product = get_product(get_the_ID());

```

Displaying a WooCommerce product price
The easiest solution for displaying a product’s price is to use the get_price_html method:

```PHP
<?php 
echo $product->get_price_html();
?>
```
Something to bear in mind when using this method is that depending on whether the product is discounted or not this code could return several different HTML structures.

We might also want to use a different method for displaying prices, such as displaying a price with or without tax. Here’s a few methods we can use for these purposes:

```PHP
$product->get_sale_price()            // – Pulls the discounted price excluding tax.
$product->get_regular_price()         // – Pulls the standard price excluding tax.
$product->get_price()                 // – Pulls the active price excluding tax (i.e. the regular or discounted price; whichever is currently applied to the product).
$product->get_price_excluding_tax()   // – Pulls the active price as above, but explicitly excludes tax.
$product->get_price_including_tax()   // – Pulls active price with tax included.
wc_price()                            // – Adds a currency symbol to a given price (the functions above will pull their respective prices without adding a currency symbol, so it must be added).
With these methods in mind, if we wanted to show the discounted price of a product with the tax included and with a currency symbol added, we could use the following code:
```

```PHP
<?php
echo wc_price($product->get_price_including_tax(1, $product->get_sale_price()));
?>
```

In the interests of clarity, it’s worth noting that the first argument of the get_price_including_tax/get_price_excluding_tax methods takes the number of products to be displayed; in our case, just the one.

Displaying product ratings
Just like the price, it’s very easy to display a product rating if required by using the get_rating_html() method:

```PHP
<?php 
echo $product->get_rating_html();
?>
```
However, in this case our layout is restricted to the standard HTML structure of the WooCommerce ratings. If we want to creating our own layout for displaying ratings we’ll need to use an alternative method to get the details, such as get_rating_count(), which pulls the total number of ratings, and get_average_rating(), which pulls the average rating of the product based on user ratings.

Displaying the number of product reviews
To display the number of reviews a product has we can use the get_review_count() method:

```PHP
<?php
echo $product->get_review_count(); ?>

```
I recommend that you pack this value into the link to the product page along with the #reviews element within it, so that immediately after clicking a link the reviews of the product will display:

```PHP
<a href="<?php the_permalink(); ?>#reviews"> Reviews: <?php echo $product->get_review_count(); ?> </a>
```

Product higlight layouts
To highlight selected products to encourage users to focus on them more we can use a variety of pre-prepared methods, such as:

```PHP
$product->is_on_sale() – To display products that are discounted.
$product->is_featured() – Displays featured products.
$product->is_in_stock() – Displays in-stock products.
$product->is_sold_individually – For products that are sold as single products, rather than as part of a pack.
$product->is_downloadable() – For products that are available as a download.
$product->is_virtual() – For digital products that are not available as a physical copy.
Here’s a quick and easy example for how to put these methods to use:
```
```PHP
<?php if($product->is_on_sale()) : ?>
<strong>
<?php _e('Sale', 'my-theme'); ?>
</strong>
<?php endif; ?>
Creating an Add to Cart button
To finish off we’ll look at the most useful element; the Add to Cart button. We’ll start by creating a basic cart:

<form class="cart" method="post" enctype="multipart/form-data">
     <input type="hidden" name="add-to-cart" value="<?php echo esc_attr($product->id); ?>">
     <button type="submit"> <?php echo $product->single_add_to_cart_text(); ?> </button>
</form>

```
As you may have gathered, the key element here is the hidden “add-to-cart” field, that holds a value equivalent to the product’s ID.

Something to remember here is that the cart used in this form may be used when we have an akax-refreshed cart in the theme, but there is a chance that the user will not see any indication that their chosen product was added to the cart.

For this reason, I personally use a redirect to the product page using an action containing the _permalink() function:

```PHP
<form class="cart" action="<?php the_permalink() ?>" method="post" enctype="multipart/form-data">
     <input type="hidden" name="add-to-cart" value="<?php echo esc_attr($product->id); ?>">
     <button type="submit"><?php echo $product->single_add_to_cart_text(); ?></button>
</form>
```
Additionally, we may also add a field to specify the total number of a given product that should be added to the cart:

```PHP
<form class="cart" action="<?php the_permalink() ?>" method="post" enctype="multipart/form-data">
     <div class="quantity">
          <input type="number" step="1" min="1" max="9" name="quantity" value="1" title="<?php _e('Szt.', 'my-theme'); ?>" class="input-text qty text" size="4">
     </div>
     <input type="hidden" name="add-to-cart" value="<?php echo esc_attr($product->id); ?>">
     <button type="submit"><?php echo $product->single_add_to_cart_text(); ?></button>
</form>
```
To finish up we should add some more conditions to the complete cart code, since the Add to Cart button shouldn’t be displayed if the product is unavailable or cannot be purchased, and if the product has multiple variants then users should be redirected to the product page. The complete code with support for these extra cases looks like this:

```PHP
<?php if($product->has_child()) : ?>
<a href="<?php the_permalink(); ?>">
<?php _e('Zobacz dostępne warianty', 'my-theme'); ?>
</a>
<?php elseif($product->is_in_stock()) : ?>
<form class="cart" action="<?php the_permalink() ?>" method="post" enctype="multipart/form-data">
     <div class="quantity">
          <input type="number" step="1" min="1" max="9" name="quantity" value="1" title="<?php _e('Szt.', 'my-theme'); ?>" class="input-text qty text" size="4">
     </div>
     <input type="hidden" name="add-to-cart" value="<?php echo esc_attr($product->id); ?>">
     <button type="submit"><?php echo $product->single_add_to_cart_text(); ?></button>
</form>
<?php endif; ?>
```

Summary
Though the above article covers several of the useful functions provided by the WC_Product class, but this is just scratching the surface of its potential. I encourage you to take the time to read through all the documentation available to ensure you have clear, up-to-date knowledge of the rest of the available fields and methods of this useful part of the WooCommerce plugin API.

Thank you!
<br /> Source : https://www.gavick.com/blog/woocommerce-product-layout 
<br /> woo : https://woocommerce.com/document/sample-products-loop/
<br /> woo git : https://github.com/woocommerce/woocommerce/wiki/wc_get_products-and-WC_Product_Query#wc_product_query-methods
<br /> woo shortcode: https://woocommerce.com/document/woocommerce-shortcodes/

