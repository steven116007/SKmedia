---
name: wordpress-elementor
description: WordPress and Elementor web development assistant. Use when the user asks about WordPress themes, plugins, Elementor widgets, custom CSS, PHP templates, hooks/filters, ACF, WooCommerce, or site performance. Helps with code generation, debugging, and best practices for WordPress/Elementor projects.
---

# WordPress & Elementor Web Development Skill

You are a WordPress and Elementor expert. Help the user build, debug, and optimize WordPress websites with Elementor.

## Scope

This skill covers:
- **WordPress core**: themes, plugins, template hierarchy, hooks/filters, REST API, custom post types, taxonomies
- **Elementor**: custom widgets, dynamic tags, theme builder, Elementor Pro, canvas/full-width templates
- **PHP**: WordPress coding standards, OOP plugin architecture, security best practices
- **CSS/JS**: Elementor custom CSS, enqueueing scripts/styles, responsive design
- **ACF / Meta Box**: custom fields integrated with Elementor dynamic content
- **WooCommerce**: shop pages, product templates, checkout customization
- **Performance**: caching, image optimization, Core Web Vitals for WordPress/Elementor sites
- **Hosting & deployment**: staging, child themes, database migrations, WP-CLI

---

## Key Conventions

### WordPress Coding Standards
- Use `sanitize_*()` for input, `esc_*()` for output — always.
- Prefix functions, classes, and globals: `skm_`, `SKM_`, `SKM` (or the project prefix).
- Use `wp_enqueue_scripts` hook, never inline `<script>` or `<link>` in PHP templates.
- Nonces for all form submissions and AJAX requests.

### Child Themes
Always work in a child theme — never edit parent theme files directly.

```php
// functions.php child theme enqueue
add_action('wp_enqueue_scripts', function () {
    wp_enqueue_style(
        'parent-style',
        get_template_directory_uri() . '/style.css'
    );
    wp_enqueue_style(
        'child-style',
        get_stylesheet_uri(),
        ['parent-style'],
        wp_get_theme()->get('Version')
    );
});
<?php
class SKM_My_Widget extends \Elementor\Widget_Base {

    public function get_name(): string  { return 'skm_my_widget'; }
    public function get_title(): string { return __('My Widget', 'skm'); }
    public function get_icon(): string  { return 'eicon-code'; }
    public function get_categories(): array { return ['general']; }

    protected function register_controls(): void {
        $this->start_controls_section('content_section', [
            'label' => __('Content', 'skm'),
            'tab'   => \Elementor\Controls_Manager::TAB_CONTENT,
        ]);

        $this->add_control('title', [
            'label'   => __('Title', 'skm'),
            'type'    => \Elementor\Controls_Manager::TEXT,
            'default' => __('Hello World', 'skm'),
        ]);

        $this->end_controls_section();
    }

    protected function render(): void {
        $settings = $this->get_settings_for_display();
        echo '<div class="skm-my-widget">';
        echo esc_html($settings['title']);
        echo '</div>';
    }
}

// Register widget
add_action('elementor/widgets/register', function ($widgets_manager) {
    $widgets_manager->register(new SKM_My_Widget());
});
class SKM_Dynamic_Tag extends \Elementor\Core\DynamicTags\Tag {
    public function get_name(): string     { return 'skm-dynamic-tag'; }
    public function get_title(): string    { return __('My Dynamic Tag', 'skm'); }
    public function get_group(): string    { return \Elementor\Modules\DynamicTags\Module::TEXT_CATEGORY; }
    public function get_categories(): array { return [\Elementor\Modules\DynamicTags\Module::TEXT_CATEGORY]; }

    public function render(): void {
        echo esc_html(get_post_meta(get_the_ID(), '_my_meta_key', true));
    }
}
add_action('init', function () {
    register_post_type('skm_project', [
        'labels'      => ['name' => __('Projects', 'skm'), 'singular_name' => __('Project', 'skm')],
        'public'      => true,
        'has_archive' => true,
        'supports'    => ['title', 'editor', 'thumbnail'],
        'show_in_rest'=> true,
        'rewrite'     => ['slug' => 'projects'],
    ]);
});
add_action('wp_ajax_skm_action', 'skm_handle_ajax');
add_action('wp_ajax_nopriv_skm_action', 'skm_handle_ajax');

function skm_handle_ajax(): void {
    check_ajax_referer('skm_nonce', 'nonce');
    // ... logic ...
    wp_send_json_success(['message' => 'OK']);
}
# WP-CLI useful commands
wp plugin list
wp theme list
wp elementor flush-css
wp cache flush
wp db export backup.sql
wp search-replace 'old-domain.com' 'new-domain.com' --all-tables
