# Decidim::Apiauth

**Provided by:** Mainio

***

## Description

This module adds JWT (JSON Web Token) based authentication to the Decidim API. It provides a new endpoint for API authentication and a method to verify the token header for each subsequent request, enabling secure communication for external applications.

The module is based on `Devise::JWT`, and its development was sponsored by the **City of Helsinki**.

***

## Installation Prerequisites

Before installing this module, ensure you have a working **Decidim application** with its standard dependencies, including **Ruby** and a configured database.

***

## Installation Instructions

Follow these steps to deploy and configure the module:

1.  **Add the gem to your Gemfile**. Open your application's `Gemfile` and add the following line:
    ```ruby
    gem "decidim-apiauth"
    ```

2.  **Install the gem and run migrations**. Execute the following commands in your terminal:
    ```bash
    bundle
    bundle exec rails decidim_apiauth:install:migrations
    bundle exec rails db:migrate
    ```

3.  **Generate and configure a secret key**. First, generate a unique secret key by running:
    ```bash
    bundle exec rails secret
    ```
    This will output a long string of characters.

4.  **Add the secret key to `secrets.yml`**. Copy the generated key and add it to your `config/secrets.yml` file. For production environments, it is strongly recommended to load the key from an environment variable.
    ```yaml
    # In config/secrets.yml

    development:
      <<: *default
      secret_key_jwt: 'paste_your_generated_key_for_development_here'

    test:
      <<: *default
      secret_key_jwt: 'paste_your_generated_key_for_testing_here'

    # For production, use an environment variable
    production:
      <<: *default
      secret_key_jwt: <%= ENV["SECRET_KEY_JWT"] %>
    ```
    Ensure the `SECRET_KEY_JWT` environment variable is set in your production environment.

***

## License

This project is licensed under the **GNU AFFERO GENERAL PUBLIC LICENSE v3.0**. See the [LICENSE-AGPLv3.txt](https://github.com/mainio/decidim-module-apiauth/blob/master/LICENSE-AGPLv3.txt) file for details.

***

## External technical resources

-   **[Source Code Repository](https://github.com/mainio/decidim-module-apiauth)**: The official GitHub repository for this module.
-   **[Devise::JWT Library](https://github.com/waiting-for-dev/devise-jwt)**: The underlying library used for JWT authentication.
-   **[Decidim Project](https://github.com/decidim/decidim)**: The main repository for the Decidim framework.
-   **API Endpoint**: The module exposes a `/api/sign_in` endpoint for token authentication.

***

## User Guide References

### API Usage

1.  **Log in to get a token**. Make a `POST` request to the `/api/sign_in` endpoint with user credentials:
    ```bash
    curl --location --request POST '[https://your-decidim-url.com/api/sign_in](https://your-decidim-url.com/api/sign_in)' \
    --form 'user[email]="your_email@example.org"' \
    --form 'user[password]="your_password"'
    ```

2.  **Use the token for authenticated requests**. The response will contain a JWT. Include this token in the `Authorization` header as a Bearer token for all subsequent API calls.
    ```bash
    curl --location --request POST '[https://your-decidim-url.com/api](https://your-decidim-url.com/api)' \
    --header 'Authorization: Bearer <paste_your_jwt_token_here>' \
    --header 'Content-Type: application/json' \
    --data-raw '{"query":"{\n  session {\n    user {\n      id\n      nickname\n    }\n  }\n}","variables":{}}'
    ```

### Advanced Configuration

To force API authentication even on public Decidim instances, add the following initializer in `config/initializers/decidim.rb`:
```ruby
# config/initializers/decidim.rb
Decidim::Apiauth.configure do |config|
  config.force_api_authentication = true
end
