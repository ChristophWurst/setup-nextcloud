# Setup Nextcloud Github Action

Set up a Nextcloud environment. Batteries included, but removable.

## Inputs

* **Optional** ``nextcloud-version``: Version tag or branch name to set up. Default `master`.
* **Optional** `install`: Run Nextcloud installation? Default `'false'`.
* **Optional** `node-version` Node.js version to set up, if any. Default `16`. Pass `false` to skip Node.js installation.
* **Optional** `php-version` PHP version to set up. Default `8.1`. Pass `false` to skip PHP installation.
* **Optional** `php-coverage` PHP coverage support, e.g. `xdebug`. Default `false`.
* **Optional** `patch-php-version-check` Remove Nextcloud version check to allow nightly PHP versions. Default `false`.
* **Optional** `tools` Additional tools to install separated by space. Available: `krankerl`. Default `''` (no tools).

## Usage

### Set up environment for unit tests

These tests only need the Nextcloud code and PHP.

```yaml
  test-unit:
    runs-on: ubuntu-latest
    name: Unit tests
    steps:
      - name: Set up Nextcloud env
        uses: ChristophWurst/setup-nextcloud@v0.2.1
        with:
          nextcloud-version: stable25
          php-version: '8.2'
          node-version: 'false' # optional but speeds up the setup
      - name: Checkout code
        uses: actions/checkout@v2
        with:
          path: nextcloud/apps/my-app
      - name: Install dependencies
        run: composer install
      - name: Run tests
        run: composer run test:unit
```

### Set up environment for integration tests

These tests need an *installed* Nextcloud and PHP.

```yaml
  test-unit:
    runs-on: ubuntu-latest
    name: Unit tests
    steps:
      - name: Set up Nextcloud env
        uses: ChristophWurst/setup-nextcloud@v0.2.1
        with:
          nextcloud-version: stable25
          php-version: '8.1'
          node-version: 'false' # optional but speeds up the setup
          install: true
      - name: Checkout code
        uses: actions/checkout@v2
        with:
          path: nextcloud/apps/my-app
      - name: Install dependencies
        working-directory: nextcloud/apps/my-app
        run: composer install
      - name: Run tests
        working-directory: nextcloud/apps/my-app
        run: composer run test:integration

```

### Set up environment for a full frontend and backend build

```yaml
  test-unit:
    runs-on: ubuntu-latest
    name: Unit tests
    steps:
      - name: Set up Nextcloud env
        uses: ChristophWurst/setup-nextcloud@v0.2.1
        with:
          nextcloud-version: stable25
          php-version: '8.1'
          node-version: 18
          install: true
          tools: 'krankerl'
      - name: Checkout code
        uses: actions/checkout@v2
        with:
          path: nextcloud/apps/my-app
      - name: Install dependencies
        working-directory: nextcloud/apps/my-app
        run: krankerl up
      - name: Package app
        working-directory: nextcloud/apps/my-app
        run: krankerl package
```
