---
# Leave the homepage title empty to use the site title
title:
date: 2022-10-24
type: landing

sections:
  - block: about.biography
    id: about
    content:
      title: Biography
      # Choose a user profile to display (a folder name within `content/authors/`)
      username: admin

  - block: features
    content:
      title: Skills
      items:
        - name: R
          description: Proficient
          icon: r-project
          icon_pack: fab
        - name: Python
          description: Proficient
          icon: python
          icon_pack: fab
        - name: Swift
          description: Intermediate
          icon: swift
          icon_pack: fab
        - name: Bayesian modeling
          description: Proficient
          icon: chart-bar  # TODO: maybe change this icon
          icon_pack: fas
        - name: Machine Learning
          description: Intermediate
          icon: project-diagram
          icon_pack: fas
        - name: Data Visualization
          description: Proficient
          icon: images
          icon_pack: fas
        - name: Linux
          description: Sufficient
          icon: linux
          icon_pack: fab
        - name: iOS, macOS & watchOS
          description: Hobbiest
          icon: apple
          icon_pack: fab
        - name: Raspberry Pi & Arduino
          description: Hobbiest
          icon: raspberry-pi
          icon_pack: fab

  # - block: experience
  #   content:
  #     title: Experience
  #     # Date format for experience
  #     #   Refer to https://wowchemy.com/docs/customization/#date-format
  #     date_format: Jan 2006
  #     # Experiences.
  #     #   Add/remove as many `experience` items below as you like.
  #     #   Required fields are `title`, `company`, and `date_start`.
  #     #   Leave `date_end` empty if it's your current employer.
  #     #   Begin multi-line descriptions with YAML's `|2-` multi-line prefix.
  #     items:
  #       - title: CEO
  #         company: GenCoin
  #         company_url: ''
  #         company_logo: org-gc
  #         location: California
  #         date_start: '2021-01-01'
  #         date_end: ''
  #         description: |2-
  #             Responsibilities include:

  #             * Analysing
  #             * Modelling
  #             * Deploying
  #       - title: Professor of Semiconductor Physics
  #         company: University X
  #         company_url: ''
  #         company_logo: org-x
  #         location: California
  #         date_start: '2016-01-01'
  #         date_end: '2020-12-31'
  #         description: Taught electronic engineering and researched semiconductor physics.
  #   design:
  #     columns: '2'

  - block: accomplishments
    id: recent-accomplishments
    content:
      # Note: `&shy;` is used to add a 'soft' hyphen in a long heading.
      title: 'Recent Accomplishments'
      subtitle:
      # Date format: https://wowchemy.com/docs/customization/#date-format
      date_format: Jan 2006
      # Accomplishments.
      #   Add/remove as many `item` blocks below as you like.
      #   `title`, `organization`, and `date_start` are the required parameters.
      #   Leave other parameters empty if not required.
      #   Begin multi-line descriptions with YAML's `|2-` multi-line prefix.
      items:
        - title: Started at Vertex Pharmaceuticals
          description: "I have joined Vertex Pharmaceuticals as a *Computational Genomics Research Scientist*."
          organization: Vertex Pharmaceuticals
          organization_url: https://www.vrtx.com/
          date_start: "2022-12-19"
          date_end: ''
          certificate_url:
          url: ''
        - title: Defended my Ph.D. dissertation
          description: "Earned my Ph.D. from Harvard Medical School with my dissertation on \"Studying the tissue-specificity of cancer drive genes through *KRAS* and genetic dependency screens.\""
          organization: Harvard University  # TODO: try to switch to harvard medical when have SVG files
          organization_url: https://hms.harvard.edu/
          date_start: '2022-11-21'
          date_end: ''
          certificate_url: 
          url: 
        - title: "`tumoroscope-pymc` Python package"
          description: "Python package of the ['Tumoroscope'](https://www.biorxiv.org/content/10.1101/2022.09.22.508914v1) model implemented in PyMC."
          organization: "PyPI"
          organization_url: "https://pypi.org"
          date_start: "2022-10-25"
          date_end: ""
          certificate_url:
          url: "https://pypi.org/project/tumoroscope-pymc/"

    design:
      columns: '1'

  - block: collection
    id: publications
    content:
      title: Publications
      filters:
        folders:
          - publication
        featured_only: false
    design:
      columns: '2'
      view: list

  - block: portfolio
    id: projects
    content:
      title: Projects
      subtitle: "[Click to see all of my projects!](/project/)"
      filters:
        folders:
          - project
      # Default filter index (e.g. 0 corresponds to the first `filter_button` instance below).
      default_button_index: 0
      # Filter toolbar (optional).
      # Add or remove as many filters (`filter_button` instances) as you like.
      # To show all items, set `tag` to "*".
      # To filter by a specific tag, set `tag` to an existing tag name.
      # To remove the toolbar, delete the entire `filter_button` block.
      buttons:
        - name: Data Analysis
          tag: 'data analysis'
        - name: Packages
          tag: package
        - name: Applications
          tag: application
        - name: Hobbies
          tag: "hobby"
    design:
      # Choose how many columns the section has. Valid values: '1' or '2'.
      columns: '2'
      view: card  # TODO: try "card"
      # For Showcase view, flip alternate rows?
      flip_alt_rows: false

  - block: collection
    id: posts
    content:
      title: Recent Posts
      subtitle: "[Click to see all of my posts!](/post/)"
      text: ''
      # Choose how many pages you would like to display (0 = all pages)
      count: 5
      # Filter on criteria
      filters:
        folders:
          - post
        author: ""
        category: ""
        tag: ""
        exclude_featured: false
        exclude_future: false
        exclude_past: false
        publication_type: ""
      # Choose how many pages you would like to offset by
      offset: 0
      # Page order: descending (desc) or ascending (asc) date.
      order: desc
    design:
      # Choose a layout view
      view: compact
      columns: '2'

  # - block: markdown
  #   content:
  #     title: Gallery
  #     subtitle: ''
  #     text: |-
  #       {{< gallery album="demo" >}}
  #   design:
  #     columns: '1'

  # - block: collection
  #   content:
  #     title: Recent Publications
  #     text: |-
  #       {{% callout note %}}
  #       Quickly discover relevant content by [filtering publications](./publication/).
  #       {{% /callout %}}
  #     filters:
  #       folders:
  #         - publication
  #       exclude_featured: true
  #   design:
  #     columns: '2'
  #     view: citation

  # - block: collection
  #   id: talks
  #   content:
  #     title: Recent & Upcoming Talks
  #     filters:
  #       folders:
  #         - event
  #   design:
  #     columns: '2'
  #     view: compact
  # - block: tag_cloud
  #   content:
  #     title: Popular Topics
  #   design:
  #     columns: '2'

  - block: contact
    id: contact
    content:
      title: Contact
      subtitle:
      text: ''
      # Contact (add or remove contact options as necessary)
      # contact_links:
      #   - icon: twitter
      #     icon_pack: fab
      #     name: DM Me
      #     link: 'https://twitter.com/Twitter'
      # Automatically link email and phone or display as text?
      autolink: true
      # Email form provider
      form:
        provider: formspree
        formspree:
          id: "xwkdepzd"
        netlify:
          # Enable CAPTCHA challenge to reduce spam?
          captcha: false
    design:
      columns: '2'
---
