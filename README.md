# Image Recognition MCP Server

A Model Context Protocol (MCP) server that provides AI-powered image recognition and description capabilities using OpenAI-compatible vision models.

## Overview

This MCP server enables AI assistants to analyze and describe images through a simple URL-based interface. It supports OpenAI's vision models as well as OpenAI-compatible local models (such as LM Studio, Ollama, etc.), providing detailed descriptions of images and making it easy to integrate image analysis capabilities into your AI workflows.

## Features

- **Image Analysis**: Analyze images from URLs and get detailed descriptions
- **Flexible Model Support**: Works with OpenAI's vision models and OpenAI-compatible local models (LM Studio, Ollama, etc.)
- **MCP Protocol**: Fully compatible with the Model Context Protocol standard
- **TypeScript**: Built with TypeScript for type safety and better development experience
- **Simple API**: Easy-to-use interface for image description requests

## Installation

### Prerequisites

- Node.js 18+
- npm or yarn
- OpenAI API key or local vision model server (e.g., LM Studio, Ollama)

### MCP Client Configuration

To use this server with an MCP client, add the following configuration:

```json
{
  "mcpServers": {
    "image-recognition": {
      "command": "npx",
      "args": ["-y", "@it-master/image-recognition-mcp"],
      "env": {
        "OPENAI_API_KEY": "your-actual-openai-api-key-here"
      }
    }
  }
}
```

To allow access to image files from any path, set `ALLOW_ALL_PATHS` to `true`:

```json
{
  "mcpServers": {
    "image-recognition": {
      "command": "npx",
      "args": ["-y", "@it-master/image-recognition-mcp"],
      "env": {
        "OPENAI_API_KEY": "your-actual-openai-api-key-here",
        "ALLOW_ALL_PATHS": "true"
      }
    }
  }
}
```

**⚠️ IMPORTANT:** The `env` section with your API key is required - this is the only way the MCP server can function. For local models, you can use any placeholder value for `OPENAI_API_KEY` and configure `OPENAI_BASE_URL` to point to your local server.

### Environment Variables

The server supports the following environment variables:

- `OPENAI_API_KEY` - Your OpenAI API key, or any placeholder value when using local models (required)
- `OPENAI_BASE_URL` - Base URL for OpenAI API or OpenAI-compatible API servers (optional, defaults to OpenAI's official API)
  - Example for LM Studio: `"http://127.0.0.1:1234/v1"`
  - Example for Ollama: `"http://localhost:11434/v1"`
- `OPENAI_MODEL` - The vision model to use for image recognition (optional, defaults to "gpt-5-mini")
  - For OpenAI: `"gpt-5-mini"`, `"gpt-4o"`, `"gpt-4o-mini"`, etc.
  - For local models: `"llava"`, `"qwen/qwen3-vl-4b"`, or any locally available vision model
- `ALLOWED_IMAGE_PATHS` - Comma-separated list of allowed local file paths (optional, defaults to "./images,./assets")
  - Example: `"./images,./assets,./downloads"`
- `ALLOW_ALL_PATHS` - Set to "true" to allow access to image files from any path. When enabled, only image file extensions (.jpg, .jpeg, .png, .gif, .webp) are allowed for security (optional, defaults to false)
- `ALLOWED_DOMAINS` - Comma-separated list of allowed URL domains for enhanced security (optional, defaults to allow all domains)
  - Example: `"example.com,cdn.example.com,images.example.org"`
  - When not set: All domains are allowed
  - When set: Only specified domains will be allowed for URL-based image requests

## Usage

### Available Tools

#### `describe-image`

Analyzes an image from a URL or local file path and provides a detailed description.

**Parameters:**

- `imageUrl` (string): The URL of the image to analyze, or a local file path
- `prompt` (string, optional): The question or prompt to ask about the image (defaults to "what's in this image?")

**Example with URL:**

```json
{
  "tool": "describe-image",
  "arguments": {
    "imageUrl": "https://example.com/image.jpg",
    "prompt": "what's in this image?"
  }
}
```

**Example with local file:**

```json
{
  "tool": "describe-image",
  "arguments": {
    "imageUrl": "./images/my-image.png",
    "prompt": "Describe the objects in this image"
  }
}
```

**Response:**

```json
{
  "content": [
    {
      "type": "text",
      "text": "The image shows a beautiful sunset over a mountain landscape with vibrant orange and pink colors in the sky..."
    }
  ]
}
```

### Integration with AI Assistants

This MCP server can be integrated with various AI assistants that support the MCP protocol, such as:

- Claude Desktop
- Other MCP-compatible AI systems

## Development

### Project Structure

```
image-recognition-mcp/
├── src/
│   ├── index.ts                # Main server implementation
│   ├── path-validator.ts       # Path validation and security functions
│   └── image-processor.ts      # Image processing utilities
├── test/
│   ├── index.test.ts           # Unit tests
│   ├── describe-image-integration.test.ts  # Integration tests
│   ├── test.png                # Test image
│   └── README.md               # Test documentation
├── dist/                       # Compiled JavaScript output
├── package.json                # Project dependencies and scripts
├── tsconfig.json               # TypeScript configuration
└── README.md                   # This file
```

### Running Tests

The project includes both unit tests and integration tests:

```bash
# Run all tests
npm test

# Run unit tests only
npm run test:unit

# Run integration tests with local OpenAI-compatible server
npm run test:integration
```

**Integration Tests Requirements:**
- A running OpenAI-compatible API server at `http://127.0.0.1:1234/v1`
- The server should support vision models (e.g., qwen/qwen3-vl-4b)
- You can use LM Studio, Ollama, or other compatible servers
- The integration tests use the `OPENAI_BASE_URL` and `OPENAI_MODEL` environment variables

The integration tests will:
- Test actual API calls to the vision model
- Verify image processing with the test image (`test/test.png`)
- Validate the complete MCP tool workflow with both default and custom prompts
- Test error handling and edge cases

### Security Features

The server includes several security features:

- **Path Validation**: Restricts local file access to allowed directories
- **Extension Validation**: Only allows specific image file extensions (.jpg, .jpeg, .png, .gif, .webp)
- **Domain Restriction**: Optional URL domain whitelist for enhanced security
- **File Existence Checks**: Validates files exist before processing

### Error Handling

The server includes robust error handling for:

- Invalid image URLs
- Unauthorized file paths or domains
- Network connectivity issues
- OpenAI API errors
- Invalid input parameters
- Unsupported file formats

## Troubleshooting

### Common Issues

**Server fails to start or doesn't work:**

- ✅ **Check if OpenAI API key is set**: This is the #1 cause of issues
  ```bash
  echo $OPENAI_API_KEY  # Should show your API key
  ```
- ✅ **Verify API key is valid**: Test with OpenAI's API directly
- ✅ **Check API key has sufficient credits**: Ensure your OpenAI account has available credits

**"Authentication failed" errors:**

- The OpenAI API key is missing or invalid
- Set the environment variable: `export OPENAI_API_KEY="your-key"`

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License. See the `LICENSE` file for details.

## Support

For support, please open an issue in the GitHub repository or contact the maintainer.
