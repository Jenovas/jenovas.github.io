---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
title: Rafael Skubisz
subtitle: Mobile Lead Developer with 12+ years of experience
---

<div class="container-fluid py-5 hero-section">
  <div class="row">
    <div class="col-lg-10 col-md-11 mx-auto text-center">
      <h2 class="welcome-message mb-4">Welcome to my professional portfolio</h2>
      <p class="lead mb-0">I specialize in mobile app development and SDKs with a focus on creating robust, 
      user-friendly solutions for complex problems. With 12+ years of experience across Android, iOS, 
      and backend technologies, I've delivered solutions used by millions of users worldwide.</p>
    </div>
  </div>
</div>

<div class="container my-5 pt-3">
  <div class="row">
    <div class="col-12 text-center mb-5">
      <h2 class="section-title">Featured Skills</h2>
      <p class="section-subtitle">Specializing in mobile development and beyond</p>
    </div>
  </div>
  
  <div class="row justify-content-center">
    <div class="col-md-6 col-lg-3 mb-4">
      <div class="card skill-card h-100">
        <div class="card-body text-center">
          <div class="skill-icon-container">
            <i class="fas fa-mobile-alt skill-icon"></i>
          </div>
          <h3>Android Development</h3>
          <p>Java / Kotlin expertise with Jetpack Compose and modern Android development practices</p>
        </div>
      </div>
    </div>
    
    <div class="col-md-6 col-lg-3 mb-4">
      <div class="card skill-card h-100">
        <div class="card-body text-center">
          <div class="skill-icon-container">
            <i class="fab fa-apple skill-icon"></i>
          </div>
          <h3>iOS Development</h3>
          <p>Swift and Objective-C experience developing native iOS applications</p>
        </div>
      </div>
    </div>
    
    <div class="col-md-6 col-lg-3 mb-4">
      <div class="card skill-card h-100">
        <div class="card-body text-center">
          <div class="skill-icon-container">
            <i class="fas fa-code skill-icon"></i>
          </div>
          <h3>SDK Development</h3>
          <p>Creating cross-platform SDKs used by major financial institutions</p>
        </div>
      </div>
    </div>
    
    <div class="col-md-6 col-lg-3 mb-4">
      <div class="card skill-card h-100">
        <div class="card-body text-center">
          <div class="skill-icon-container">
            <i class="fas fa-server skill-icon"></i>
          </div>
          <h3>Backend Skills</h3>
          <p>Ruby on Rails, Java/Kotlin backends, and blockchain development</p>
        </div>
      </div>
    </div>
  </div>
</div>

<div class="container my-5 py-3">
  <div class="row">
    <div class="col-lg-6 mb-4">
      <div class="card h-100">
        <div class="card-body d-flex flex-column">
          <h2 class="section-title text-left mb-4">About Me</h2>
          <p>I'm a mobile lead developer with a passion for creating technology that improves people's lives. With over a decade of experience in Android, iOS, and backend development, I've built solutions used by millions of users worldwide.</p>
          <p>Born in the U.S. and now living in Poland, I bring a unique perspective to my work and embrace challenges that push the boundaries of what's possible in mobile development.</p>
          <div class="mt-auto">
            <a href="/about/" class="btn btn-primary">
              <i class="fas fa-user mr-2"></i> Learn More About Me
            </a>
          </div>
        </div>
      </div>
    </div>
    
    <div class="col-lg-6 mb-4">
      <div class="card h-100">
        <div class="card-body d-flex flex-column">
          <h2 class="section-title text-left mb-4">Professional Experience</h2>
          <p>I currently lead mobile development at Alkami Technology, where I've developed an Android and iOS SDK used by major U.S. digital banking platforms, serving hundreds of financial institutions with 10 million monthly active users.</p>
          <p>My expertise spans mobile app architecture, SDK development, and team leadership, with a focus on delivering secure, high-performance solutions.</p>
          <div class="mt-auto">
            <a href="/cv/" class="btn btn-primary">
              <i class="fas fa-file-alt mr-2"></i> View My Professional CV
            </a>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>

<div id="blog-section" class="container my-5 py-3">
  <h2 class="section-title">Recent Posts</h2>
  <p class="section-subtitle">Latest thoughts and updates</p>

  <div class="row">
    {% assign posts = site.posts | sort: 'date' | reverse %}
    {% for post in posts limit:3 %}
    <div class="col-lg-4 mb-4">
      <div class="card h-100">
        <div class="card-body">
          <span class="tag">{{ post.date | date: "%B %d, %Y" }}</span>
          <h3 class="card-title">{{ post.title }}</h3>
          <p class="card-text">{{ post.excerpt | strip_html | truncatewords: 25 }}</p>
          <a href="{{ post.url | relative_url }}" class="btn btn-outline-primary btn-sm mt-auto">Read More</a>
        </div>
      </div>
    </div>
    {% endfor %}
    
    {% if site.posts.size == 0 %}
    <div class="col-12 text-center">
      <p>No posts available yet. Check back soon!</p>
    </div>
    {% endif %}
  </div>
  
  {% if site.posts.size > 3 %}
  <div class="text-center mt-4">
    <a href="/blog/" class="btn btn-primary">
      <i class="fas fa-pen-fancy mr-2"></i> View All Posts
    </a>
  </div>
  {% endif %}
</div>

<div class="container-fluid py-5 cta-section">
  <div class="row">
    <div class="col-lg-8 mx-auto text-center">
      <h2 class="mb-4">Let's Connect</h2>
      <p class="lead mb-4">Interested in working together? Feel free to get in touch!</p>
      <div class="d-flex justify-content-center flex-wrap">
        <a href="mailto:skubisz.rafael@gmail.com" class="btn btn-primary m-2 px-4">
          <i class="fas fa-envelope mr-2"></i> Contact Me
        </a>
        <a href="https://github.com/Jenovas" target="_blank" class="btn btn-outline-primary m-2 px-4">
          <i class="fab fa-github mr-2"></i> GitHub
        </a>
        <a href="https://www.linkedin.com/in/rafael-skubisz-210993b0/" target="_blank" class="btn btn-outline-primary m-2 px-4">
          <i class="fab fa-linkedin mr-2"></i> LinkedIn
        </a>
      </div>
    </div>
  </div>
</div>

<style>
  .hero-section {
    background-color: var(--dark-surface);
    border-radius: 8px;
    margin-bottom: 4rem;
    padding: 4rem 0;
  }
  
  .cta-section {
    background-color: var(--dark-surface);
    border-radius: 8px;
    margin-top: 4rem;
    padding: 4rem 0;
  }
  
  .welcome-message {
    color: var(--primary-color);
    font-weight: 600;
    font-size: 2rem;
  }
  
  .skill-card {
    transition: transform 0.3s ease, box-shadow 0.3s ease;
    border: 1px solid var(--dark-border);
  }
  
  .skill-card:hover {
    transform: translateY(-10px);
    box-shadow: 0 10px 20px rgba(0, 0, 0, 0.2);
    border-color: var(--primary-color);
  }
  
  .skill-icon-container {
    background-color: rgba(79, 140, 255, 0.1);
    width: 100px;
    height: 100px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    margin: 0 auto 1.5rem;
    border: 2px solid var(--primary-color);
  }
  
  .skill-icon {
    font-size: 3rem;
    color: var(--primary-color);
  }
  
  .section-title.text-left {
    text-align: left;
  }
  
  @media (max-width: 767px) {
    .hero-section, .cta-section {
      padding: 3rem 0;
    }
    
    .skill-icon-container {
      width: 80px;
      height: 80px;
    }
    
    .skill-icon {
      font-size: 2.5rem;
    }
    
    .welcome-message {
      font-size: 1.75rem;
    }
  }
</style>

