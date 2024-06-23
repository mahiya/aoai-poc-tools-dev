# RAG チャットアプリの構築

以下の様な、Azure OpenAI Service x Azure AI Search での RAG を用いた質問回答ができるチャットアプリを構築することができます。
![RAG チャットアプリ](.images/deploy-rag-chat-app.gif)

Azure 構成図は以下の通りです。
![RAG チャットアプリの構成図](.images/deploy-rag-chat-app.jpg)

## 構築方法

### 1. Azure OpenAI Service アカウントの作成
以下の Microsoft ドキュメントに従い、回答生成とテキスト埋め込み生成のための LLM サービスを構築します。  
アカウントのリージョンは、"gpt-4o"モデルと"text-embedding-3-large"モデルの両方をデプロイすることができる ```East US``` リージョンを用いることをお勧めします(上記両モデルがデプロイできるリージョンであれば、他のリージョンでも問題ありません)  
> [操作方法: Azure OpenAI Service リソースを作成してデプロイする - Azure OpenAI | Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/ai-services/openai/how-to/create-resource?pivots=webportal)  


### 2. Azure AI Search アカウントの作成
以下の Microsoft ドキュメントに従い、RAG質問回答におけるデータソースを格納するための検索エンジンを構築します。  
> [ポータルで Search サービスを作成する - Azure AI Search | Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/search/search-create-service-portal)  

### 3. Azure Storage アカウントの作成
以下の Microsoft ドキュメントに従い、Azure AI Search に格納するファイル(PDF ファイルまたは Office ファイル(.docx, .pptx, .xlsx))を格納するための Blob ストレージを構築します。  
> [ストレージ アカウントを作成する - Azure Storage | Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/storage/common/storage-account-create?tabs=azure-portal)

### 4. (任意) Bing Search API リソースの作成
もし回答生成の情報源として Bing Search API ([Bing Web Search API](https://learn.microsoft.com/en-us/bing/search-apis/bing-web-search/overview), [Bing News Search API](https://learn.microsoft.com/en-us/bing/search-apis/bing-news-search/overview)) を使用する場合は、以下の Microsoft ドキュメントに従い、Azure Marketplace でリソースを作成します。  
> [Create Bing Search Services Resource - Bing Search Services | Microsoft Learn](https://learn.microsoft.com/ja-jp/bing/search-apis/bing-web-search/create-bing-search-service-resource)

### 5. Azure AI Search へのデータ入力環境の構築
[mahiya/document-intelligence-search-skill](https://github.com/mahiya/document-intelligence-search-skill) の [README.md](https://github.com/mahiya/document-intelligence-search-skill/blob/main/README.md) で記されている内容に従い、Azure AI Search へ Azure Storage に格納したファイルをインデクサーを通じて入力し、かつ Azure Functions と Azure AI Document Intelligence で OCR 分析・テキスト抽出・チャンク分割を行い、Azure OpenAI Service で埋め込みを取得して、セマンティックハイブリッド検索(フルテキスト検索 x ベクトル検索 x セマンティックランキング)を可能にさせます。

### 6. チャットアプリの構築
[mahiya/sample-rag-chat-app](https://github.com/mahiya/sample-rag-chat-app) の [README.md](https://github.com/mahiya/sample-rag-chat-app/blob/main/README.md) で記されている内容に従い、Azure App Service にチャットアプリケーションを構築します。このチャットアプリケーションは、Azure AI Search や Bing Search API を情報源として、ユーザからの質問に回答することができます。