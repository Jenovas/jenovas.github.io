---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
title: Rafael Skubisz
subtitle: Software Engineering Manager with 12+ years of experience
eyebrow: "PORTFOLIO"
hero_cta:
  - label: "Read the CV →"
    href: "/cv/"
    style: "btn-primary"
  - label: "Contact"
    href: "mailto:skubisz.rafael@gmail.com"
    style: "btn-outline-primary"
---

<section class="section-block container">
  <span class="section-block__num">§ 01</span>
  <h2 class="section-block__title">Featured Skills</h2>
  <p class="section-block__lede">A small set of things I do well.</p>
  <hr class="section-block__rule">

  <div class="row justify-content-center">
    <div class="col-md-6 col-lg-3 mb-4">
      <div class="skill-card">
        <div class="skill-icon-container">
          <i class="fas fa-mobile-alt skill-icon"></i>
        </div>
        <h3>Android Development</h3>
        <p>Java / Kotlin expertise with Jetpack Compose and modern Android development practices</p>
      </div>
    </div>
    <div class="col-md-6 col-lg-3 mb-4">
      <div class="skill-card">
        <div class="skill-icon-container">
          <i class="fab fa-apple skill-icon"></i>
        </div>
        <h3>iOS Development</h3>
        <p>Swift and Objective-C experience developing native iOS applications</p>
      </div>
    </div>
    <div class="col-md-6 col-lg-3 mb-4">
      <div class="skill-card">
        <div class="skill-icon-container">
          <i class="fas fa-code skill-icon"></i>
        </div>
        <h3>SDK Development</h3>
        <p>Creating cross-platform SDKs used by major financial institutions</p>
      </div>
    </div>
    <div class="col-md-6 col-lg-3 mb-4">
      <div class="skill-card">
        <div class="skill-icon-container">
          <i class="fas fa-server skill-icon"></i>
        </div>
        <h3>Backend Skills</h3>
        <p>Ruby on Rails, Java/Kotlin backends, and blockchain development</p>
      </div>
    </div>
  </div>
</section>

<section class="section-block container">
  <span class="section-block__num">§ 02</span>
  <h2 class="section-block__title">About / Experience</h2>
  <p class="section-block__lede">Two stories that overlap.</p>
  <hr class="section-block__rule">

  <div class="row">
    <div class="col-lg-6 mb-4">
      <div class="card card--hero h-100">
        <div class="card-body">
          <h3 class="card-title">About Me</h3>
          <p>I'm a mobile lead developer with a passion for creating technology that improves people's lives. With over a decade of experience in Android, iOS, and backend development, I've built solutions used by millions of users worldwide.</p>
          <p>Born in the U.S. and now living in Poland, I bring a unique perspective to my work and embrace challenges that push the boundaries of what's possible in mobile development.</p>
          <div class="mt-auto">
            <a href="/about/" class="btn btn-outline-primary">Learn More About Me</a>
          </div>
        </div>
      </div>
    </div>
    <div class="col-lg-6 mb-4">
      <div class="card card--hero h-100">
        <div class="card-body">
          <h3 class="card-title">Professional Experience</h3>
          <p>I'm currently the Software Engineering Manager at Pulsate, where I lead a team of 5 engineers while also contributing hands-on development (~30-50% of my time). I've developed an Android and iOS SDK used by major U.S. digital banking platforms, serving hundreds of financial institutions with 10 million monthly active users.</p>
          <p>My expertise spans mobile app architecture, SDK development, team leadership, and mentorship, with a focus on delivering secure, high-performance solutions while fostering growth in my team.</p>
          <div class="mt-auto">
            <a href="/cv/" class="btn btn-outline-primary">View My Professional CV</a>
          </div>
        </div>
      </div>
    </div>
  </div>
</section>

<section class="section-block container" id="blog-section">
  <span class="section-block__num">§ 03</span>
  <h2 class="section-block__title">Recent Posts</h2>
  <p class="section-block__lede">Latest thoughts and updates.</p>
  <hr class="section-block__rule">

  <div class="row">
    {% assign posts = site.posts | sort: 'date' | reverse %}
    {% for post in posts limit:3 %}
    <div class="col-lg-4 mb-4">
      <a class="card card--linked h-100" href="{{ post.url | relative_url }}">
        <div class="card-body">
          <p class="post-card__meta">{{ post.date | date: "%b %d %Y" | upcase }}</p>
          <h3 class="card-title">{{ post.title }}</h3>
          <p class="card-text">{{ post.excerpt | strip_html | truncatewords: 25 }}</p>
          <span class="btn-link-arrow mt-auto">Read more <span class="arrow">→</span></span>
        </div>
      </a>
    </div>
    {% endfor %}
    {% if site.posts.size == 0 %}
    <div class="col-12 text-center">
      <p>No posts available yet. Check back soon!</p>
    </div>
    {% endif %}
  </div>

  {% if site.posts.size > 3 %}
  <div class="mt-4">
    <a href="/blog/" class="btn btn-outline-primary">View all posts</a>
  </div>
  {% endif %}
</section>

<section class="section-block container">
  <span class="section-block__num">§ 04</span>
  <h2 class="section-block__title">Let's Connect</h2>
  <p class="section-block__lede">Interested in working together? Get in touch.</p>
  <hr class="section-block__rule">

  <div class="card card--hero card--accent">
    <div class="card-body">
      <div class="d-flex flex-wrap" style="gap: var(--space-3);">
        <a href="mailto:skubisz.rafael@gmail.com" class="btn btn-primary">Contact Me</a>
        <a href="https://github.com/Jenovas" target="_blank" rel="noopener" class="btn btn-outline-primary">GitHub</a>
        <a href="https://www.linkedin.com/in/rafael-skubisz-210993b0/" target="_blank" rel="noopener" class="btn btn-outline-primary">LinkedIn</a>
      </div>
    </div>
  </div>
</section>
