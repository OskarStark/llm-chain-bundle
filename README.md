# LLM Chain Bundle

Symfony integration bundle for [php-llm/llm-chain](https://github.com/php-llm/llm-chain) library.

## Installation

```bash
composer require php-llm/llm-chain-bundle:@dev php-llm/llm-chain:@dev
```

## Configuration

```yaml
# config/packages/llm_chain.yaml
llm_chain:
    runtimes:
        azure_gpt:
            type: 'azure'
            base_url: '%env(AZURE_OPENAI_BASEURL)%'
            deployment: '%env(AZURE_OPENAI_GPT)%'
            api_key: '%env(AZURE_OPENAI_KEY)%'
            version: '%env(AZURE_OPENAI_VERSION)%'
        azure_embeddings:
            type: 'azure'
            base_url: '%env(AZURE_OPENAI_BASEURL)%'
            deployment: '%env(AZURE_OPENAI_EMBEDDINGS)%'
            api_key: '%env(AZURE_OPENAI_KEY)%'
            version: '%env(AZURE_OPENAI_VERSION)%'
        openai:
            type: 'openai'
            api_key: '%env(OPENAI_API_KEY)%'
    llms:
        azure_gpt:
            runtime: 'azure_gpt'
        original_gpt:
            runtime: 'openai'
    embeddings:
        azure_embeddings:
            runtime: 'azure_embeddings'
        original_embeddings:
            runtime: 'openai'
    stores:
        chroma_db:
            type: 'chroma-db'
            collection_name: '%env(CHROMA_COLLECTION)%'
        azure_search:
            type: 'azure-search'
            api_key: '%env(AZURE_SEARCH_KEY)%' 
            endpoint: '%env(AZURE_SEARCH_ENDPOINT)%'
            index_name: '%env(AZURE_SEARCH_INDEX)%' 
            api_version: '2024-07-01'
```

## Usage

### Chain Service

Use the simple chat service to leverage GPT:
```php
use PhpLlm\LlmChain\Chat;

final readonly class MyService
{
    public function __construct(
        private Chain $chain,
    ) {
    }
    
    public function submit(string $message): string
    {
        $messages = new MessageBag();
        $messages[] = Message::forSystem('Speak like a pirate.');
        $messages[] = Message::ofUser($message);
        
        return $this->chain->call($messages);
    }
}
```

### Register Tools

You can register tools by using the `AsTool` attribute and enable the chain to execute it:
```php
use PhpLlm\LlmChain\ToolBox\AsTool;

#[AsTool('company_name', 'Provides the name of your company')]
final class CompanyName
{
    public function __invoke(): string
    {
        return 'ACME Corp.'
    }
}
```

### Profiler

The profiler panel provides insights into the chain's execution:

![Profiler](./profiler.png)
