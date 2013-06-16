World Flags CSS Sprite
==================

There were several world flag sprites out there already, but none of them really served my purposes. Specifically
their flags were too small, and many of the flag images had been altered with some kind of styling. These sprites have
been created from images scraped from Wikipedia's flags, so should be unaltered.

The sprites in this repository are:

- Uniform in their height, but variable in their width. (To create a uniform width variable height sprite, see the next section)
- CSS rules use country's English name, not it's country code.

If either of those limitations won't work for you, see the following section to create your own sprite.


# Create Your Own Sprite

1. Choose one of the functions below, and run it one time. This will create a 'flags' directory that contains all the world's Soverign State flags, limited by the dimensions you chose.
3. Scan through the files you've downloaded. These are pretty dumb functions, so you might've downloaded a few extra wikipedia images. Delete these.
3. Compress your 'Flags' directory into a .zip file.
4. Go to http://spritegen.website-performance.org/ and upload your flags.zip file
5. Congrats, you've now got a World Flags CSS sprite in the size you need.

The following two functions scape images from Wikipedia's list of Sovereign State Flags. World flags are not always the same proportions, so the first function will limit the flags' dimensions to a maxium height, and the second function will limit the flags' dimensions to a maximum width. Be forewarned that these functions were created at 2am on very little caffeine, so they're not very smart. After you've run the functions and downloaded the images, have a quick look through the 'flags' directory you've just created to see if you've downloaded any extra Wikipedia images you don't want.

## Maximum Height

Use this function if you want to scrape all images, limiting their dimensions by a maximum height

```PHP

function scrape_wikipedia_flags_max_height( $max_height = 60 ) {

  // Create a 'flags' directory inside whatever directory we're working in.
    // We'll download our flags here
    $dir = realpath(dirname(__FILE__)) . "/flags/";
    if(!is_dir($dir)) mkdir($dir,0777,true);

  // set this to something ridiculous because we only care about the max height here
    $max_width = 10000;

    $html = file_get_contents('http://commons.wikimedia.org/wiki/Sovereign-state_flags');
    $dom = new domDocument;
    $dom->loadHTML($html);
    $dom->preserveWhiteSpace = false;
    $images = $dom->getElementsByTagName('img');

    foreach ($images as $image) {

          $src = str_replace("//upload.", "http://upload.", $image->getAttribute('src'));

        $old_size = getimagesize($src);

        // Get current dimensions
        $old_width  = $old_size[0];
        $old_height = $old_size[1];

        // Calculate the scaling we need to do to fit the image inside our frame
        $scale      = min($max_width/$old_width, $max_height/$old_height);

        // Get the new dimensions
        $new_width  = ceil($scale*$old_width);

        // My it's 2am and I'm tired way of changing the size of the SVG
        $src = explode("/", $src);
        $filename = array_pop($src);
        $src[] = $new_width . strstr($filename, 'px');
        $src = implode("/",$src);

        // The country's name is stored in the image's ALT tag
        $name = $image->getAttribute('alt');

        // We'll name our image according to the country's name, put it all lower case, and replace spaces with underscores
        $img =  $dir . str_replace(' ', '_', strtolower( $name ) ) . ".png";
        file_put_contents($img, file_get_contents($src));
    }

    // You probably only want to run this function once, so die
    die('done');
}
```
## Maximum Width

Use this function if you want to scrape all images, limiting their dimensions by a maximum width

```PHP

function scrape_wikipedia_flags_max_width( $max_width = 60 ) {

  // Create a 'flags' directory inside whatever directory we're working in.
  // We'll download our flags here
    $dir = realpath(dirname(__FILE__)) . "/flags/";
    if(!is_dir($dir)) mkdir($dir,0777,true);

    // set this to something ridiculous because we only care about the max width here
    $max_height = 10000;

    $html = file_get_contents('http://commons.wikimedia.org/wiki/Sovereign-state_flags');
    $dom = new domDocument;
    $dom->loadHTML($html);
    $dom->preserveWhiteSpace = false;
    $images = $dom->getElementsByTagName('img');

    foreach ($images as $image) {

          $src = str_replace("//upload.", "http://upload.", $image->getAttribute('src'));

        // My it's 2am and I'm tired way of changing the size of the SVG
        $src = explode("/", $src);
        $filename = array_pop($src);
        $src[] = $max_width . strstr($filename, 'px');
        $src = implode("/",$src);

        // The country's name is stored in the image's ALT tag
        $name = $image->getAttribute('alt');

        // We'll name our image according to the country's name, put it all lower case, and replace spaces with underscores
        $img =  $dir . str_replace(' ', '_', strtolower( $name ) ) . ".png";
        file_put_contents($img, file_get_contents($src));
    }

    // You probably only want to run this function once, so die
    die('done');
}
```