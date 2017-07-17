### Barrel Development Best Practices

# Guide to Constructing an Efficient ReadMe Doc

Good documentation should provide details on how to sufficiently setup and deploy a site for both localized development and production environments.

###Format
1. Use GFM README.md in root of project.
2. Use code blocks/fences and inline code highlighting.
3. Use appropriate and consistent headings.
4. Use numbered and/or unordered list styling for procedural details.

###Dependencies (Production)
- Identify (list) dependencies to install a production instance.

###Installation
1. Detail how to install production dependencies.
2. Detail how to install application/framework/platform.
3. Do include sample config files.
4. If possible, create build and deployment scripts. TBD.

###Migration (if applicable)
- Link to DropBox or other cloud-based storage platform for all assets such as non-tracked configuration, media, and database scripts.

###App/Server Configuration
1. Detail any server-specific configuration such as .htaccess.
2. Detail any application-specific configuration that is not a production task.

###Production Tasks
- Identify any production tasks that cannot be completed via a database migration (or other script).

###Development Setup and Workflow (Development dependencies)
1. Detail how to setup a complete development environment workflow.
2. Identify any package managers such as Composer, PEAR, needed for development.
3. Grunt Workflow (or other)
  1. Utilize common build targets to promote easy portability and broader dev coverage.
  2. Identify non-standard build targets.

##Project-Specific Details and/or Notes.
1. Identify any specific environment variables, constants, etc that a developer or project maintainer should be aware of.
2. Identify any non-standard, unusual, or obscure aspects that might help a developer or system admin.
