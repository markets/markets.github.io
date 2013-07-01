---
layout: post
title:  Download file via Ajax request
date:   2013-07-01
---

Everything retrieved via Ajax goes into javascript "memory" space. This is because JavaScript cannot interact with disk. That would be a security issue. Ajax is not designed to do this kind of stuff. But as always, there are some tricks ... Here is a simple approach of how to get it in a Ruby on Rails based application.

### Scenario
Imagine a plugin embeded in a web page that provides a Base64 image encoded. This image should be generated and stored in the server side and then perform an automatic download.

### Tech Context
Back-end: Ruby on Rails. Front-end: Jquery.

### Solution
Use two methods (actions) in your controllers layer. The first one receives the image (a string representing Base64 codification), decodes this string and writes it into filesystem:

{% highlight ruby %}
  # POST /images
  def create
    file_path = File.join(IMAGES_PATH, "awesome_image_name.jpeg")
    file = File.new(file_path, 'wb')
    file.write(Base64.decode64(params[:base64_string]))
    file.close

    render json: { image_basename: File.basename(file) }
  end
{% endhighlight %}

The second action will be responsible for the download:

{% highlight ruby %}
  # GET /images/download?image_basename=image_name
  def download
    file_path = File.join(IMAGES_PATH, params[:image_basename])
    file = File.open(file_path)

    send_file file.path, type: 'image/jpeg', x_sendfile: true
  end
{% endhighlight %}

Finally, the Javascript code to interact with these server actions. The idea is to make a `POST` request to create the image. Then, using the 'success' callback, send a request to second action (with file name as a parameter in this case) in order to perform the download. Do this via `document.location.href` (open a new browser window is also valid) and file will start downloading:

{% highlight javascript %}
function downloadImageBase64(image) {
  $.ajax({
    type: 'POST',
    url: '/images',
    data: { base64_string: image },
    success: function(response){
      document.location.href = '/images/download?image_basename=' + 
                               response.image_basename;
    }
  });
}
{% endhighlight %}

The key here is the Javascript part. Use your favorite language/framework in the server side to decode, write and send the file.