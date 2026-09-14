Install Dependencies
Ensure your environment has Node.js and npm installed to run the logger.

Verify Node.js is installed: node -v
Verify npm is installed: npm -v

# 1. Start the request logger
npm run request-logger

# 2. Choose your agent
# (You'll be prompted to select which agent you want to run)

# 3. Choose your model provider
# (Pick the provider you want to log requests for, e.g. OpenAI, Anthropic, etc.)

# 4. Start the agent through the logger
# This ensures all traffic goes through your local logger instead of directly to the provider

# 5. Inspect the logs
# Each interaction creates a file in ./request-logger/logs
# Open these files to see the full request and response, including:
#   - Conversation history
#   - Prompts sent to the model
#   - Tool calls and their results
