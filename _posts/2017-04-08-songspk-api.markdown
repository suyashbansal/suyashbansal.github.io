---
layout: post
title:  "Songs.PK API "
date:   2017-04-08 15:36:00 +0530
---
A few months back, I was thinking about making an application which could be a simpler, ad-free version of the original SongsPK website. But, like for any application, I needed data. Since there was no API available for the purpose, I started working on my own.

I used [PHP][php-home] for scripting and hosted the code on [Heroku][heroku]. I have an instance of the code running at [`https://songspk-suyash.herokuapp.com/`][heroku-project]. But please keep in mind that since I'm using the free version of Heroku, it might take some time to respond at first.

### How it works?

1. Fetches the website content via `curl`
2. Uses `preg_match_all` function to extract the necessary details from the content
3. Return the data as JSON encoded data

{% highlight php %}
<?php
$curl = curl_init();
curl_setopt ($curl, CURLOPT_URL, "http://songspk.io/");
curl_setopt($curl, CURLOPT_RETURNTRANSFER, 1);
$html = curl_exec ($curl);
curl_close ($curl);

$slider = array();

preg_match_all("/leftrightslide\[(\d)\]='(.*?)'/", $html, $matches, PREG_SET_ORDER);

foreach ($matches as $key) {
  preg_match_all("/href=\"(.*)\".*src=\"(.*)\".*alt=\"(.*)\"/", $key[2], $matches2, PREG_SET_ORDER);
  unset($matches2[0][0]);
  $slider[$key[1]] = array('link' => $matches2[0][1], 'image' => $matches2[0][2], 'name' => substr($matches2[0][3], 0, -10));
}
echo json_encode($slider);
?>
{% endhighlight %}

### How to use?

Let's take an example to know how we can use the API.

Suppose you want to access the latest releases from the home page, just access: `https://songspk-suyash.herokuapp.com/api`

Now lets say you have the link to a album page and want all the details of that album with song names and download links. You just have to send the page link via GET method: 
`https://songspk-suyash.herokuapp.com/api/page.php?page=http://www.songspk.io/mp3/indian_songs/baar_baar_dekho_mp3_songs_download` 

This will give you somethink like:

{% highlight json %}
{
"details": {
	"1": "Baar Baar Dekho",
	"2": "2016",
	"3": "Sidharth Malhotra, Katrina Kaif, Sarika, Ram Kapoor, Taaha Shah, Sayani Gupta, Rohan Joshi",
	"4": "Amaal Malik, Badshah, Bilal Saeed and various"
},
"songs": [{
	"songname": "Kho Gaye Hum Kahan",
	"artist": "Jasleen Kaur Royal, Prateek Kuhad",
	"link": "http:\/\/sound30.mp3slash.net\/indian\/baarbaardekho\/01 - Kho Gaye Hum Kahan [Songs.PK].mp3"
}, {
	"songname": "Sau Aasmaan",
	"artist": "Armaan Malik, Neeti Mohan",
	"link": "http:\/\/sound30.mp3slash.net\/indian\/baarbaardekho\/02 - Sau Aasmaan [Songs.PK].mp3"
}, ... 
{% endhighlight %}

Now you can use this data anywhere you want. 

You can find the source code for the SongsPK API at:
{% include icon-github.html username="suyashbansal" %} /
[SongsPK-API](https://github.com/suyashbansal/SongsPK-API)

I know that this is not perfect. So if you do some improvements, please send me a pull request! :smile:

[php-home]: http://www.php.net/
[heroku]:   https://www.heroku.com/
[heroku-project]: https://songspk-suyash.herokuapp.com/
