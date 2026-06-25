---
title: "Automation Tools"
weight: 10
description: "A manager's tour of the automation tools worth knowing — curl, CLIs, GitHub Actions, Google Apps Script, Ansible, and Terraform — with concrete recipes."
lastUpdated: 2026-06-25
goDeeper:
  - group: Books
    title: "Automate the Boring Stuff with Python — Al Sweigart"
    url: "https://automatetheboringstuff.com"
    why: "The friendliest on-ramp to scripting away repetitive work."
  - group: Tools
    title: "GitHub Actions documentation"
    url: "https://docs.github.com/actions"
    why: "Official reference for building automation into your pipeline."
  - group: Tools
    title: "Zapier"
    url: "https://zapier.com"
    why: "No-code glue between tools when you don't want to write a script."
---

As an engineering leader, time is your scarcest resource. Any task you do more than a couple of times is a candidate for automation — so before you grind through it again, ask whether a tool could do it for you.

Below is a high-level tour of the automation tools I reach for most, with concrete recipes. The examples use the free online REST API at [jsonplaceholder.typicode.com](https://jsonplaceholder.typicode.com), so you can run them as-is.

{{< protip >}}
Automate to demonstrate value, not just to save keystrokes. A small script that surfaces team health data nobody had before is worth more than one that shaves five seconds off a task you do once a week.
{{< /protip >}}

## curl

[curl](https://curl.se) is a command-line tool for getting or sending data — including files — using [URL](https://en.wikipedia.org/wiki/URL) syntax. Most machines already have it. To check, open a terminal and run `curl --help`.

A few recipes worth memorizing:

```bash
# Pull a response straight into the terminal
curl https://jsonplaceholder.typicode.com/posts/1

# Download a response into a file (then read it with cat)
curl -o test.txt https://jsonplaceholder.typicode.com/posts
cat test.txt

# Send data with a POST
curl --data "title=Hello&body=Hello world" https://jsonplaceholder.typicode.com/posts

# Update data with a PUT
curl -X PUT -d "title=Hello" https://jsonplaceholder.typicode.com/posts/3

# Delete data with a DELETE
curl -X DELETE https://jsonplaceholder.typicode.com/posts/3

# Authenticate on routes that require it
curl -u carrie:12345 https://example.com

# Follow redirects (http://google.com → https://www.google.com)
curl -L http://google.com

# Upload / download a file over FTP
curl -u name@example.com:12345! -T hello.txt ftp://ftp.example.com
curl -u name@example.com:12345! -O ftp://ftp.example.com/hello.txt
```

A couple of flags worth knowing:

- `-X` (or `--request`) specifies the HTTP request method.
- `-d` (or `--data`) sends data in a POST request. There are [more examples here](https://www.warp.dev/terminus/curl-post-request).

### Pulling stats from Jira or GitHub

You can use curl to pull stats out of GitHub or Jira when a proper tool isn't available. That said, try first to buy something off the shelf — a tool like [Jellyfish's Team Health](https://jellyfish.co/solutions/team-health) already joins Jira and GitHub data in a meaningful UX, and your time is better spent elsewhere.

For Jira, create a Personal Access Token (PAT) on the [API tokens page](https://id.atlassian.com/manage-profile/security/api-tokens), then use it directly:

```bash
curl --request GET \
  --url 'https://your-domain.atlassian.net/rest/api/3/issue/{issueIdOrKey}' \
  --user 'email@example.com:<api_token>' \
  --header 'Accept: application/json'
```

Or, more compactly, piping through `jq -C` for colorful JSON:

```bash
curl -u email@example.com:<api_token> https://your-domain.atlassian.net/rest/api/3/issue/FAM-493 | jq -C
```

If you want to use that token in code rather than on the command line, you'll need to Base64-encode it. For more ideas, browse this collection of [Jira shell scripts](https://github.com/sparxsys/jira-shell-scripts).

## CLIs

Command-line interfaces are a clean way to script against third-party tools. The [GitHub CLI](https://cli.github.com) is a great example — its [full command reference is here](https://cli.github.com/manual).

First, authenticate. The docs cover [`gh auth login --with-token`](https://cli.github.com/manual/gh_auth_login), including reading a token from a file. For automation inside a pipeline, [this post on gh auth in GitHub Actions](https://josh-ops.com/posts/gh-auth-login-in-actions) walks through the options.

```bash
gh auth login --with-token < token.txt

curl -H "Authorization: Bearer $GH_TOKEN" \
  -s "https://api.github.com/search/issues?q=type:pr+author:$AUTHOR+user:KonaCoder+created:>=2022-07-01" | jq '.total_count'
```

What's happening in that second command:

- `-s` (or `--silent`) disables the progress meter.
- `-H` (or `--header`) adds an extra header to the request.
- `| jq` pretty-prints the JSON, which makes it readable — no more pasting output into a text editor to format it. Learn `jq` from its [manual](https://jqlang.github.io/jq/manual) or [this helpful walkthrough](https://codehannah.nyc/posts/jq).
- Appending `'.total_count'` narrows the output to just that one key.

## GitHub Actions

[GitHub Actions](https://docs.github.com/actions) lets you build automation right into your pipeline — for example, labeling PRs based on the number of files and lines changed.

Using the [Pull Request Size Labelling action](https://github.com/marketplace/actions/pull-request-size-labelling), drop a workflow file at `.github/workflows/pr_size_labeling.yml`:

```yaml
name: "Pull Request Size Labelling"
on:
  pull_request:
    types:
      - opened
      - reopened
      - synchronize
jobs:
  addLabels:
    runs-on: ubuntu-latest
    steps:
      - uses: JoshwJB/pull-request-size-labelling-probot@v1.2.1
        env:
          GITHUB_TOKEN: ${{ secrets.API_CALLS_TOKEN }}
```

Now every PR gets a size label automatically — which gives you a concrete, data-backed way to coach engineers toward smaller, more iterative pull requests.

## Google tools

You can automate a surprising amount inside Google Workspace.

From Google Docs or Sheets, open **Extensions → [Apps Script](https://script.google.com)** to build reusable tasks. For example, here's a [Jira REST API call inside an Apps Script for Google Sheets](https://github.com/chrisurban/jira-sprint-reporting/blob/master/general-query.gs).

In Google Docs, typing `@` exposes a wealth of [smart chips and building blocks](https://support.google.com/docs/answer/10710316):

- `@date` inserts a date chip
- `@name` inserts a person chip
- `@titleOfMeeting` + Tab creates meeting notes for the selected event

For something more advanced, you can build a [Google Apps Script web app](https://developers.google.com/apps-script/guides/web) to securely pass in a token when you need to share the automation with others.

## Ansible

[Ansible](https://www.ansible.com) is an open-source, command-line IT automation tool written in Python. It can configure systems, deploy software, and orchestrate workflows for application deployment, system updates, and more — a good way to demonstrate value and deliver real efficiencies for your organization.

## Terraform

[Terraform](https://www.terraform.io) lets you automate infrastructure on any cloud.

One management-friendly use case: define consistent Datadog [monitors](https://registry.terraform.io/providers/DataDog/datadog/latest/docs/resources/monitor) across teams so observability isn't a snowflake per squad. Datadog's own [guide to managing Datadog with Terraform](https://www.datadoghq.com/blog/managing-datadog-with-terraform) shows how.
