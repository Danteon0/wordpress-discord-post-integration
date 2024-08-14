<!-- Social Media Links -->
<p align="center">
  <a href="https://discord.com/invite/eWcNKXmsgw" target="_blank">
    <img src="https://img.shields.io/badge/Discord-%2300b0ff?style=for-the-badge&logo=discord&logoColor=white" alt="Discord" />
  </a>
  <a href="https://emirhankaya.net" target="_blank">
    <img src="https://img.shields.io/badge/Website-%23000000?style=for-the-badge&logo=google-chrome&logoColor=white" alt="Website" />
  </a>
  <a href="https://x.com/Danteon0" target="_blank">
    <img src="https://img.shields.io/badge/Twitter-%231DA1F2?style=for-the-badge&logo=twitter&logoColor=white" alt="X" />
  </a>
</p>

[![Discord Presence](https://lanyard.cnrad.dev/api/496393095282294796)](https://discord.com/users/496393095282294796)

# 🤖 WordPress Discord Post Integration

This project is an example of an integration used to automatically share new blog posts published on your WordPress site in a Discord channel. This way, every new post is automatically sent to the Discord channel you specify, allowing your followers to access the latest content instantly.

## 📜 Prerequisites

To use this integration, you need the following requirements:

- **WordPress**: This project is designed to work on a WordPress site. You should have an up-to-date version of WordPress.
- **Discord Account**: You need a Discord account to implement the integration. You also need a Discord server where you want the messages to appear.
- **Web Server**: Your WordPress site needs to be hosted on a web server.

## 📁 Creating a Discord Webhook

In Discord, you need to create a webhook to automatically share your WordPress posts. The webhook provides a URL to send messages to your Discord server. Here are the steps to create a webhook:

1. **Log in to Discord Server**: Open your Discord application and select the relevant server.
2. **Create Webhook**: Go to the server settings, click on the "Integrations" tab, and select "Create Webhook".
3. **Copy the Webhook URL**: Copy the URL provided for the created webhook. This URL will be used in the WordPress integration code.

## 📕 WordPress Integration

After creating your Discord webhook, you can use it to share posts on your WordPress site. Follow these steps to implement the integration:

1. **Update the functions.php File**: Add the following code to the `functions.php` file in your WordPress theme files.

## 📗 Code for functions.php

Below is an example PHP code that will automatically send new posts to your Discord channel in WordPress. This code integrates WordPress post submissions to Discord.

```php
<?php

function send_to_discord($title, $author, $date, $content, $link, $image_url, $webhook_url, $author_avatar) {
    $embed = array(
        "title" => $title,
        "url" => $link,
        "description" => $content,
        "color" => hexdec("7289da"), //Color code you want in the embed
        "footer" => array(
            "text" => "example.com is subject to copyright",
        ),
        "image" => array(
            "url" => $image_url,
        ),
        "author" => array(
            "name" => $author,
            "icon_url" => $author_avatar,
        ),
        "timestamp" => $date,
    );

    $data = array(
        "content" => "New Blog Post Published! @everyone",
        "embeds" => array($embed),
    );

    $options = array(
        'http' => array(
            'header'  => "Content-type: application/json\r\n",
            'method'  => 'POST',
            'content' => json_encode($data),
        ),
    );

    $context  = stream_context_create($options);
    $result = file_get_contents($webhook_url, false, $context);

    if ($result === FALSE) {
        error_log('An error occurred while sending the Discord webhook.');
    }
}

function discord_webhook_new_post($post_ID, $post) {
    // Return if already notified
    if (get_post_meta($post_ID, '_discord_notified', true)) {
        return;
    }

    // Run only for published posts
    if ($post->post_status !== 'publish' || $post->post_type !== 'post') {
        return;
    }

    $webhook_url = 'WEBHOOK_URL';
    $title = get_the_title($post_ID);
    $author = get_the_author_meta('display_name', $post->post_author);
    $author_avatar = get_avatar_url($post->post_author);
    $date = get_the_date('c', $post_ID);
    $content = wp_trim_words(get_the_content(null, false, $post_ID), 40, '...');
    $link = get_permalink($post_ID);
    $image_url = '';

    if (has_post_thumbnail($post_ID)) {
        $image_url = get_the_post_thumbnail_url($post_ID);
    }

    send_to_discord($title, $author, $date, $content, $link, $image_url, $webhook_url, $author_avatar);
    update_post_meta($post_ID, '_discord_notified', true);
}

function on_save_post($post_ID, $post, $update) {
    // Run only for published posts
    if ($post->post_status === 'publish' && $post->post_type === 'post') {
        // Add a short delay to ensure the image is fully loaded
        wp_schedule_single_event(time() + 5, 'send_to_discord_event', array($post_ID, $post));
    }
}

add_action('send_to_discord_event', 'discord_webhook_new_post', 10, 2);
add_action('save_post', 'on_save_post', 10, 3);
```

 2. **Update the Webhook URL:**
 - Replace `YOUR_DISCORD_WEBHOOK_URL` with the webhook URL you created.

## 📕 Sending a Message
You are now ready to send messages! You can use the `send_to_discord` function to send instant messages to your Discord channel from anywhere.
```php
send_to_discord($title, $author, $date, $content, $link, $image_url, $webhook_url, $author_avatar);
```

## 📕 Considerations
**Security**: Do not share your webhook URL with anyone.
**Debugging**: If the message does not send, check your curl settings and webhook URL. For more support, you can join our Discord server.

## ✉️ Help and Support
If you encounter any issues with this integration or need additional assistance, you can get support by joining our Discord server or by going to the GitHub Issues page and creating a support request.

## ✒️ Contributing
If you would like to contribute to this project, please follow these steps:

- Fork the project.
- Make your changes.
- Send a pull request.

## 📑 License
This project is licensed under the MIT License. For more information, please refer to the License File.
