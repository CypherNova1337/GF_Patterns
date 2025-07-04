This repository contains a curated and massively expanded collection of `gf` (grep-friend) patterns, rewritten from the ground up for the modern web. While the original patterns are legendary, the web has evolved. This project brings them up to speed to help bug bounty hunters and security researchers find more vulnerabilities, faster.

## The Philosophy: Beyond Simple Matching

The core goal of this project was not just to add more keywords, but to fundamentally rethink how we find interesting things in code, logs, and HTTP traffic. Our methodology is based on three key principles:

### 1. From Presence to Extraction

Many older patterns would only confirm the *presence* of a potential vulnerability. We've rewritten them to **extract the most valuable information**.

**Old Way (url-params.json):**
- **Pattern:** `[?].*[&]?`
- **Result:** Finds the entire URL string, creating noise.
- `https://example.com/search.php?id=123&user=admin&page=5`

**New Way:**
- **Pattern:** `(?:[?&;])([a-zA-Z0-9_.-]+)=`
- **Result:** Uses `grep -o` to extract only the parameter names.
- `id`
- `user`
- `page`

This gives you a clean, actionable wordlist for fuzzing and analysis.

### 2. High-Confidence, Format-Based Detection

Modern secrets have specific, identifiable formats. Instead of just looking for the word "key," our new patterns match the actual structure of the secrets themselves. This drastically reduces false positives and finds keys that keyword matching would miss.

**Old Way (github-secrets.json):**
- **Pattern:** `github.*['"][0-9a-zA-Z]{35,40}['"]`
- **Result:** Finds the word "github" near any long string. Prone to false positives.

**New Way:**
- **Pattern:** `ghp_[0-9a-zA-Z]{36}|gho_[0-9a-zA-Z]{36}|...`
- **Result:** Finds only strings that match the exact, unique prefixes and structure of modern GitHub tokens (`ghp_`, `gho_`, etc.). This is the same method GitHub uses for its own secret scanning.

### 3. Comprehensive Coverage for the Modern Stack

The web isn't just PHP and jQuery anymore. We've added hundreds of new keywords and entire new pattern files to cover the modern technology landscape.

- **Cloud Providers:** AWS, Google Cloud (GCP), Azure.
- **SaaS & APIs:** Stripe, Twilio, Slack, Mailgun, SendGrid, etc.
- **Modern Frameworks:** React, Vue, Next.js, FastAPI, Spring Boot.
- **DevOps & Infrastructure:** Docker, Kubernetes, Terraform, Jenkins.
- **New Frontiers:** A brand new `ai.json` pattern to find keywords related to AI/ML services, frameworks (PyTorch, TensorFlow), and vector databases (Pinecone, Weaviate).

## How to Use

Getting started with these patterns is simple. `gf` automatically looks for pattern files in a hidden directory in your home folder called `~/.gf`.

1.  **Clone this repository to your local machine:**
    ```bash
    git clone https://github.com/CypherNova1337/GF_Patterns
    ```

2.  **Navigate into the cloned directory:**
    ```bash
    cd GF_Patterns
    ```

3.  **Create the `.gf` directory in your home folder if it doesn't already exist:**
    ```bash
    mkdir -p ~/.gf
    ```

4.  **Copy all the `.json` pattern files into the `.gf` directory:**
    This is the crucial step. By placing the pattern files here, `gf` will be able to find and use them.
    ```bash
    cp *.json ~/.gf/
    ```
    *Note: If you cloned the repository to a different location, make sure to adjust the path in the `cp` command.*

5.  **Verify the installation:**
    You can list the available patterns to make sure they were loaded correctly.
    ```bash
    gf -list
    ```
    You should see `ai`, `aws-keys`, `xss`, and all the other new patterns in the output.

6.  **Start finding secrets!**
    Now you can pipe data to `gf` and use the name of the pattern file (without the `.json` extension).
    ```bash
    # Example: Find potential XSS parameters in a list of URLs

    cat urls.txt | gf xss

    # Example: Find AWS keys in a directory of source code

    grep -rhE '.' /path/to/code/ | gf aws-keys
    ```

## The Patterns

This collection includes rewritten and expanded patterns for:

| Category                  | Examples                                     |
| ------------------------- | -------------------------------------------- |
| **Secrets & API Keys** | `aws-keys`, `google-keys`, `stripe`, `github`  |
| **Vulnerabilities** | `sqli`, `xss`, `ssrf`, `lfi`, `rce`, `ssti`    |
| **Technology Fingerprints** | `fw` (frameworks), `servers`, `jsvar`          |
| **Interesting Files** | `secret-ext`, `interesting-ext`, `debug-pages` |
| **New Additions** | `ai`, `crypto`, `jwt`                          |

## Contributing

This is a living project. The web will continue to evolve, and so should these patterns. Contributions are highly encouraged!

If you have an idea for a new pattern or an improvement to an existing one, please feel free to:
1.  Fork the repository.
2.  Create a new branch (`git checkout -b feature/new-amazing-pattern`).
3.  Add or edit your pattern file.
4.  Submit a pull request with a clear description of your changes.

## Disclaimer

These patterns are provided for educational and authorized security testing purposes only. Do not use these tools to scan networks or systems you do not have explicit permission to test. The user is responsible for their own actions.
