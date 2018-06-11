# Deploy React App to Apache CentOS

* Step 1: Go to your `packages.json` folder and put this line of code to match your real domain address.<br>
  `"homepage": "https://yourwebsite.com/deploy_folder/",`
* Step 2: Run build command with `npm` as follow.
  ```
  cd my-app
  npm run build
  ```
* Step 3: Create `.htaccess` file in `build` folder with below snippet. 
  ```
  Options -MultiViews
  RewriteEngine On
  RewriteCond %{REQUEST_FILENAME} !-f
  RewriteRule ^ index.html [QSA,L]
  ```
* Step 4: Deploy your `build` folder to `deploy_folder`. DONE
