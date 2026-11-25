# mm24blog

A Laravel blog with a local SQLite database. The steps below use Windows PowerShell. The project commands also work on macOS and Linux once the same tools and PHP extensions are installed.

## 1. Install VS Code and Git

Run the installers for your operating system. On Windows, select **Add to PATH** in the VS Code installer and keep the Git installer option that makes Git available from the command line.

- [VS Code download](https://code.visualstudio.com/download)
- [Git installers](https://git-scm.com/downloads)

## 2. Clone the repository

Open a new terminal in the folder where you want to keep the project. The clone creates a folder named `blog`.

```sh
git clone https://github.com/klaccdeduska/blog.git
cd blog
code .
```

If the `code` command is unavailable, open VS Code and select **File → Open Folder**, then choose the `blog` folder.

## 3. Install PHP 8.5

On 64-bit Windows, download the **x64 Non Thread Safe** PHP 8.5 ZIP and extract it to `C:\php`, so that `C:\php\php.exe` exists. Install the Visual C++ Redistributable linked on the PHP downloads page if it is missing. Search Windows for **Edit environment variables for your account**, edit the user **Path**, and add `C:\php`. Open a new terminal. [PHP for Windows](https://www.php.net/downloads.php?os=windows)

On macOS or Linux, use the PHP installation instructions for your operating system and install PHP 8.5 with CLI support. [PHP installation manual](https://www.php.net/manual/en/install.php)

```sh
php -v
php --ini
```

## 4. Configure PHP extensions

Edit the file shown as **Loaded Configuration File** by `php --ini`. If it shows `(none)` for the Windows ZIP installation, create the configuration file from the supplied development template.

```powershell
Copy-Item C:\php\php.ini-development C:\php\php.ini
```

In the Windows `php.ini`, set `extension_dir` to the directory containing the extension DLLs. Find the following extension lines and remove the leading `;` to enable them. Keep only one active entry for each extension.

```ini
extension_dir = "C:\php\ext"
extension=curl
extension=fileinfo
extension=mbstring
extension=openssl
extension=pdo_sqlite
extension=sqlite3
extension=zip
```

PDO and the XML modules (`dom`, `libxml`, `xml`, and `xmlwriter`) are included in the standard Windows build. On macOS and Linux, use your PHP distribution's matching extension packages if any of the required modules are missing. Check the active configuration and loaded modules after editing.

```sh
php --ini
php -m
```

## 5. Install Composer

On Windows, run **Composer-Setup.exe** and select `C:\php\php.exe` when asked for the PHP executable. On macOS or Linux, follow the command-line installation instructions and put Composer on your PATH. Open a new terminal and check the installation. [Composer installation instructions](https://getcomposer.org/doc/00-intro.md#installation-windows)

```sh
composer -V
```

## 6. Install Bun and Node.js

Install Bun. In Windows PowerShell, run the official installer below. On macOS or Linux, use the installer for your platform. [Bun installation instructions](https://bun.sh/docs/installation)

```powershell
irm https://bun.sh/install.ps1 | iex
```

Install **Node.js 24 LTS** for the development tools. Open a new terminal and verify both commands. [Node.js download](https://nodejs.org/en/download)

```sh
bun --version
node --version
```

## 7. Install project dependencies

From the cloned `blog` folder, the one containing `artisan`, run the following commands. Bun creates a local `bun.lock` from the checked-in npm lockfile so Laravel can detect Bun when starting the development server.

```sh
composer install
bun install
```

## 8. Create the local environment file

Copy `.env.example` to `.env` on the first setup. In PowerShell, run this command.

```powershell
Copy-Item .env.example .env
```

On macOS or Linux, use this command.

```sh
cp .env.example .env
```

Keep `DB_CONNECTION=sqlite` in `.env` to use the local SQLite database. Keep your local `.env` out of Git.

## 9. Generate the application key

```sh
php artisan key:generate
```

This writes a new `APP_KEY` to `.env`.

## 10. Create and migrate the database

From the project root, create the SQLite file if it is missing, then run the migrations. The file and database tables live locally under `database/`.

```sh
php -r "if (!file_exists('database/database.sqlite')) { touch('database/database.sqlite'); }"
php artisan migrate
```

If migration reports `could not find driver`, check that `pdo_sqlite` appears in the output of `php -m` and rerun the migration.

## 11. Start the blog

```sh
composer run dev
```

Keep the terminal open and visit [http://localhost:8000](http://localhost:8000). The blog is running when the page loads and its styles are visible. If port 8000 is busy, use the address printed by the server. Press **Ctrl+C** in the terminal to stop the development processes.
