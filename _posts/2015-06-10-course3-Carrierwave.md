---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 7: File Uploading with Carrierwave"
tags: tealeaf
---
##Adding Carrierwave to your Application

####1. Install gem Carrierwave-aws
####2. Make sure you have ImageMagick installed locally

####3. Run migration to remove current small/large image urls and add columns for images

####4. Mount uploaders to video model
{%highlight ruby%}
mount_uploader :large_cover, LargeCoverUploader
mount_uploader :small_cover, SmallCoverUploader
{%endhighlight%}

####5. Create app/uploaders directory, add uploaders to folder:
{%highlight ruby%}
#app/uploaders/large_cover_uploader.rb
class LargeCoverUploader < class LargeCoverUploader < CarrierWave::Uploader::Base
  include CarrierWave::MiniMagick

  version :large_version do
    process resize_to_fill: [665, 375]
  end
end
{%endhighlight%}
Note: That specifiying version is required since uploading the same file, although it is being resized for the large or small cover photo will cause it to be overidden.


You will also need to specificy the version in the views:
{%highlight ruby%}
#videos/show.html.haml
%article.video
  .container
    .row
      .video_large_cover.col-sm-7.col-sm-offset-1
        = image_tag @video.large_cover.large_version.url, alt: "large video"

{%endhighlight%}

{%highlight ruby%}
#app/uploaders/small_cover_uploader.rb
class SmallCoverUploader < CarrierWave::Uploader::Base
  include CarrierWave::MiniMagick

  process :resize_to_fill => [166, 236]
end
{%endhighlight%}

####6. Create config/initializers/carrier_wave.rb
Add configuration based on [docs](https://github.com/carrierwaveuploader/carrierwave#using-amazon-s3)


####7. Follow this blog using [S3 IAMs](http://blog.danielle.tuckerlabs.com/post/60491757671/creating-a-simple-aws-s3-bucket-with-iam-access)

####8. Update the User Policy to include the following configurations:
{%highlight ruby%}
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "NotAction": "iam:*",
      "Resource": "*"
    }
  ]
}
{%endhighlight%}

####9. Now you need to store your credentials in Rails Env variables. Follow this [Tealeaf blogpost](http://www.gotealeaf.com/blog/managing-environment-configuration-variables-in-rails)



