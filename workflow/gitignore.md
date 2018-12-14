# See https://help.github.com/ignore-files/ for more about ignoring files.

### Ignore all Files
*~
/*

### Ignore files from MAC
.DS_Store

### Ignore sass cache
.sass-cache

### Whith npm ignore node modules
node_modules/

### With composer ignore vendor
vendor

### Except these files.
!*.md
!composer.*
!.travis.yml
!/.gitignore
!/bitbucket-pipelines.yml

# Wordpress themes

### Except "wp-content"
!/wp-content

### Ignore everything in the "wp-content" directory
wp-content/*

# Except themes and plugins
!/wp-content/themes/
!/wp-content/plugins/

### Ignore everything in the themes and plugins directories
/wp-content/themes/*
/wp-content/plugins/*

### Except our theme and plugin
!/wp-content/themes/name-of-the-theme
!/wp-content/plugins/

# React projects

### Expo
.expo/

### Misc
.env.local
.env.development.local
.env.test.local
.env.production.local

npm-debug.log*
yarn-debug.log*
yarn-error.log*

.idea/
android/
ios/
e2e/

# Testing
/coverage

# Production
/build


# Other Dependencies
/.pnp
.pnp.js