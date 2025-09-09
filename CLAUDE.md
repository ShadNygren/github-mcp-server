# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is the GitHub MCP Server - a Model Context Protocol (MCP) server that connects AI tools directly to GitHub's platform. It provides AI agents with the ability to read repositories, manage issues and PRs, analyze code, and automate workflows through natural language interactions.

The server is written in Go and supports both remote (hosted by GitHub) and local deployment options.

## Essential Commands

### Development
- **Build the project**: `go build -v ./cmd/github-mcp-server`
- **Run tests**: `script/test` or `go test -race ./...`
- **Lint code**: `script/lint` (auto-installs golangci-lint if needed, runs gofmt first)
- **Generate documentation**: `script/generate-docs`

### Testing
- **Unit tests**: `go test -race ./...` (uses testify for assertions)
- **E2E tests**: Located in `e2e/` directory (see e2e/README.md for details)
- **Update tool snapshots**: `UPDATE_TOOLSNAPS=true go test ./...` (when schema changes)

## Architecture

### Core Structure
- **`cmd/github-mcp-server/`**: Main application entry point and CLI setup
- **`pkg/github/`**: GitHub API integration and tool implementations
- **`pkg/toolsets/`**: Tool organization by functional groups (issues, repos, actions, etc.)
- **`internal/ghmcp/`**: Core MCP server implementation
- **`internal/toolsnaps/`**: Tool schema snapshot testing utility

### Tool Organization
Tools are organized into toolsets that can be enabled/disabled:
- `context`: User and GitHub context (strongly recommended)
- `repos`: Repository operations
- `issues`: Issue management
- `pull_requests`: PR operations
- `actions`: GitHub Actions/CI-CD
- `code_security`: Security scanning tools
- `notifications`: GitHub notifications
- `discussions`: GitHub Discussions
- `gists`: Gist management
- `users`, `orgs`: User and organization tools

### Key Dependencies
- `github.com/mark3labs/mcp-go`: MCP protocol implementation
- `github.com/google/go-github/v74`: GitHub REST API client
- `github.com/shurcooL/githubv4`: GitHub GraphQL API client
- `github.com/spf13/cobra`: CLI framework
- `github.com/stretchr/testify`: Testing framework
- `github.com/migueleliasweb/go-github-mock`: GitHub API mocking

### Configuration Options
- **Toolset filtering**: `--toolsets repos,issues,pull_requests` or `GITHUB_TOOLSETS` env var
- **Read-only mode**: `--read-only` or `GITHUB_READ_ONLY=1`
- **Dynamic toolsets**: `--dynamic-toolsets` or `GITHUB_DYNAMIC_TOOLSETS=1`
- **Custom GitHub host**: `--gh-host` or `GITHUB_HOST` (for GHE)
- **Tool descriptions**: Override via `github-mcp-server-config.json` or `GITHUB_MCP_*` env vars

## Testing Philosophy

- **Unit tests**: Located alongside implementation files (`*_test.go`)
- **Table-driven tests**: Preferred for behavioral testing
- **Schema snapshots**: All tool schemas are snapshot-tested to prevent breaking changes
- **Mocking**: Uses go-github-mock for GitHub API simulation
- **Test structure**: Tool snapshot → Schema validation → Behavioral tests

## Important Files

- **`.golangci.yml`**: Linter configuration with security-focused rules
- **`docs/testing.md`**: Comprehensive testing documentation
- **`script/`**: Development helper scripts
- **`e2e/README.md`**: End-to-end testing guide