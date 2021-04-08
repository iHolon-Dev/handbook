# A common set of development norms

Adhering to a common set of developer norms is a vital factor when a team of people is trying to produce high quality, timely, business critical software.

I'm regularly asked to work on codebases that do not conform to any known measure of coding standards.

Issues include, but are not limited to:

* no linting
* no, or few, or inadequate levels of automated testing
* no common README format
* out of date or simply innacurate README docs
* no common CONTRIBUTING standards
* no standards for PR, branch, and commit message naming
* too many branches, making it impossible for a new developer to know where to start
* poor choice of repository or project names
* repositories scattered across multiple accounts
* lack of enforcement of CI/CD use

Coding standards are, by their very nature, opinionated, and the following represents my own opinion, based on decades of professional software development, of what our common coding standards ought to be.  These standards are mostly independent of programming language (I've included some language specific recommendations as well however) and present a 'best-practice' approach to developing high quality software.

## Repository Structure

1. For any codebase there will be a repository, known as the 'upstream' repository.

    The benefits of enforcing this are as follows

    1. When a new developer joins the project it's easy for them to see immediately which branch contains the latest work in progress and which branch contains the latest release code.
    2. For public facing repositories it presents a clean and consistent public face for our codebase, showing us in the best possible light.
    3. Each developer can use their own fork to do whatever work they need to, and the onus is on them to keep their own repositories tidy.

2. The Upstream Repository will only have two branches, `develop`, containing the latest working code, and `master` containing the current release.
3. The default branch will be `develop`
4. The `develop` and `master` branches are locked down such that the only way code can be contributed to them is via a peer-reviewed pull-request.
5. Over and above the default labels provided by GitHub add `documentation` and `feature` labels.
6. Assign teams `admins` with admin rights, and `developers` with write access to the repo.

## Workflow

For work in progress developers will use the `forked-git-flow` process, namely they will

1. Fork the `upstream` repository into their own personal accounts, clone the fork to your local machine, and set up the `upstream` as a remote.

        git remote add upstream git@github.com:<repo>.git
        git remote set-url --push upstream no_push

2. Create a feature branch. (I use the `git-flow` command-line tools to do this, see below for discussion of branch, PR and commit message naming) `git flow feature start ...`

    Developers should not, as a general rule be working in other developer's branches, but if it's really needed they can by setting up another `git remote`
3. do work
4. ensure work is accompanied by tests
5. ensure test coverage is not decreased by the work
6. ensure work conforms to common linting standards (For Javascript projects I use eslint and prettier for this, but tools vary from language to language)
7. ensure work is consistent with current documentation
8. when work is done, publish the branch to your personal fork (`git flow feature publish ...`)
9. using GitHub to create a Pull Request against the upstream `develop` branch (see below for PR naming scheme)
10. assign reviewers, appropriate labels, and assign the PR to yourself
11. wait for CI/CD to run and give a green light
12. notify devs on slack that the PR is awaiting review
13. respond to any review comments / make changes as appropriate. (developer can simply `git push` their alts to update the same PR)
14. when all changes / PR is approved, merge the PR and delete the branch
15. `git checkout develop` then `git pull upstream develop`, then `git push` to ensure your fork is in sync with the upstream.

## CI/CD configuration

We use a mix of TravisCI and CircleCI for our CI/CD needs.  Whichever we use (each have their own pros and cons but are essentially similar, so this advice is agnostic) the CI/CD must be configured to

1. run linter
2. run unit tests
3. run integration tests
4. deploy to staging (in the case of a run against `develop`), or production (in the case of a run against `master`)
5. Notify Slack / Jira

## Naming things

### Features

Features must be named per the following pattern `#{issue number}/{some_descriptive-text}` — so for example, if you are working on issue `ABC-1` with the title "do the thing", call your feature `ABC-1/do_the-thing`. Obviously use your common sense to avoid making the feature names too long.

Note this will creating a feature via `git flow` will create a branch called `feature/{issue number}/{some_descriptive-text}`.

### Commit Messages

When committing something use the `-m` flag to add a short commit message of the format `{issue number} summary of what you changed`.  So for example if you are working on issue `ABC-1` and you added a method to the `aardvark_controller` you might use the following commit message `"ABC-1 added anteater method to aardvark controller"`

Commit messages ought to be in the past tense.

In general try to group file changes wherever appropriate, so if your controller change also involved updating something in a helper file, the one commit message can happily encompas the changes to both files. The message ought to reflect the main aim of the change.

### Pull Requests

Pull requests must be named as follows `[issue type, issue number] high level description of change`.  The following Issue Types are recognised

* `Bug Fix` - the change fixes a bug
* `Feature` - the change adds a new feature (the usual issue type)
* `Documentation` — The change is a documentation only change
* `Optimisation` - The change is an optimisation of the code base without any functional changes

If your change does not fit any of these categories, use `Feature`. Likewise if your change is not tied to an issue number you may use `n/a` instead.

So to use the above example your Pull Request would be named `[Feature, ABC-1] added anteater to aardvark`

## README format

Every project needs a file `README.md` written in Github Flavoured Markdown, with sections as follows

1. A short overview of the project
2. Current CI/CD status for both `develop` and `master` branches (Both Circle and Travis provide badges for this)
3. Prerequisites and expectied prior knowlede including links to associated repositories
4. Useage instructions
5. Development instructions

    1. How to build,
    2. How to test
    3. Any specific environment variables needed

6. Deployment instructions
7. Contributing nodes (ususally just an instructon to see CONTRIBUTING.md)

## Linting

For Javascript projects I use [`eslint`](https://eslint.org) and [`prettier`](https://prettier.io) to enforce a common coding style, configured via the following base `.eslint.json` file. This can be enhanced with React specific plugins as required, but overall stick to the defaults but with enforced single quotes and no trailing semi-colons.

    {
      "extends": [
        "standard",
        "prettier",
        "prettier/standard"
      ],
      "plugins": [
        "prettier",
        "standard",
        "mocha"
      ],
      "parserOptions": {
        "sourceType": "module"
      },
      "env": {
        "es6": true,
        "node": true,
        "mocha": true
      },
      "rules": {
        "prettier/prettier": ["error", { "singleQuote": true, "semi": false }]
      }
    }

For solidity projects I use [`solhint`](https://protofire.github.io/solhint/) with the following configuration

    {
      "extends": "default",
      "rules": {
        "compiler-fixed": false,
        "indent": ["error", 4],
        "quotes": ["error", "single"],
        "max-line-length": ["error", 120]
      }
    }

## Use Docker for external dependencies

For code that requires external dependencies such as Mongo, Redis, Postgres, etc, ensure there is a `docker-compose.yml` file configured to run those dependencies.  Do not assume that a developer has Mongo etc already installed.  If the developer is a contractor with multiple clients it's often difficult or impossible for them to run such things on their bare metal.

## Other services

In addition to using standard CI/CD such as Travis or Circle

1. Use codecov.io to QA code coverage
2. Use Greenkeeper to keep dependencies up to date
3. For Javascript projects try and avoid using `grunt` or other dated build systems and stick to basic `npm` commands within the `package.json` file and use Webpack for bundling and for asset pipeline management
5. Avoid use of transpilers such as Babel for pure Node services such as API servers or libraries. They complicate release management.  Instead ensure you are using a modern version of Node (9.7 or better) which includes almost all of the modern ES6 (and some ES7) features such as object spread notation out of the box.
