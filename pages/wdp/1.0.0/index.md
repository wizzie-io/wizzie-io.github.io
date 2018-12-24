---
title: WDP 1.0.0
permalink: /wdp_1.0.0_index.html
toc: false
---

# How to add content to this repo

First of all, create your own folders inside pages/ folder. You can create folders as you want because the folder names are not used. This is just for organize your docs. Jekyll will loop over all files regardless of their folders. Check normalizer example in order to understand the organization.

Each file .md file containing your docs must start with a header like this:

```

---
title: Normalizer Scalability
permalink: /normalizer_0.7.2_scalabilty.html
toc: false
---

```

The title will be rendered in your page. The permalink MUST NOT contains "/" in order to support relative links.
Use the proposed way: "service_version_linkname" --> "/normalizer_0.7.2_scalabilty.html"
This name (normalizer_0.7.2_scalabilty.html) is the name that jekyll use when it render the site (you can check it at _site (folder containing rendered files))

# Service versions

In order to support service versioning, you should create a new folder under your product with the version (check normalizer folder). Each of these folders contains the docs related to that version. Ensure that each .md file permalink match the version. DO NOT USE SLASHES!.
After adding your content and check that all permalinks are ok, create a new sidebar for that version in _data/sidebars. Check normalizer examples. These sidebars will be used when you change your version. Ensure each sidebar links to the correct permalink.

Then, add your new sidebar to "sidebars" at \_config.yml. Sidebars can not contains "." so define your versions with "\_".

Now, create a new topnav for your service if it does not exist. If it exists, edit it and add the new version. Check normalizer example.

Finally, go to _config.yml and add the correct scopes at defaults section:

```

  -
    scope:
      path: "pages/my-service/my.version"
      type: "pages"
    values:
      layout: "page"
      comments: true
      search: true
      sidebar: my.service_my_version_sidebar
      topnav: my-service_topnav

```

Doing that, you ensure all content files you create use your own sidebar.

Done!!


# WDP versions

In order to support a new WDP version, you should create a new folder inside pages/wdp/ with the new version name like pages/wdp/2.0.0 and add all your content.

After that, you should add a new sidebar at "_data/sidebars/" like wdp_2.0.0_sidebar.yml fixing all service versions. 
Then, add the new version to wdp_topnav.yml in order to add it to the dropdown menu.
Finally, go to _config.yml and do two steps:

1.Set:
```

  -
    scope:
      path: ""
      type: "pages"
    values:
      layout: "page"
      comments: true
      search: true
      sidebar: wdp_1_0_0_sidebar
      topnav: wdp_topnav

```      
To the new version.

2.Add a new scope for specific new wdp version:

```

  -
    scope:
      path: "pages/wdp/2.0.0"
      type: "pages"
    values:
      layout: "page"
      comments: true
      search: true
      sidebar: wdp_2_0_0_sidebar
      topnav: wdp_topnav
      
```
      
In this way, when a user enters to home, it will be at 2.0.0 (new version) but, when the new user changes the version in the dropdown menu, all the content will be set to that selected version.

Done!!


# Using internal relative links

If you want to use relative links (e.g. you want to link from one of your pages to another page), you should use the link as:


```

[Multi Tenant](/normalizer_{{"{{page.version"}}}}_multi_tenant)

```

page.version is a variable declared in the front matter. You can declare all variables you want. `version` is not used by jekyll. Is used by my content in order to avoid changes when I publish a new version. In that way I only need to update all front matters without changing every link.


``` 

---
title: Normalizer Stream Configuration
version: 0.7.3
permalink: /normalizer_0.7.3_stream_configuration.html
toc: true
---

```