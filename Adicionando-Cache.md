#Adicionando cache na sua API Gateway

- Adicione a dependência do pacote Ocelot.Cache.CacheManager ao seu projeto
- Configure o Ocelot com a opção de cache
- Configurar o middleware do cache do Ocelot


## Adicionando dependência

Abra o Gerenciador de Pacotes NuGet e instale o pacote Ocelot.Cache.CacheManager. Isso pode ser feito através do comando Install-Package Ocelot.Cache.CacheManager no Console do Gerenciador de Pacotes NuGet ou adicionando a dependência no arquivo csproj.

## Configurando cache

* A seção "FileCacheOptions" define as opções de cache para a rota.
* O tempo de vida do cache é definido em segundos na opção "TtlSeconds".
* O nome da região do cache é definido na opção "Region"
* O tipo de cache é definido na opção "Type" (Neste exemplo, o tipo de cache é Memory.)

como mostra o exemplo abaixo.

```
{
  "Routes": [
    {
      "DownstreamPathTemplate": "/api/{everything}",
      "DownstreamScheme": "https",
      "UpstreamPathTemplate": "/{everything}",
      "UpstreamHttpMethod": [ "get", "post", "put", "delete" ],
      "AuthenticationOptions": {
        "AuthenticationProviderKey": "Bearer",
        "AllowedScopes": [ "api1.read", "api1.write" ]
      },
      "DownstreamHostAndPorts": [
        {
          "Host": "localhost",
          "Port": 5000
        }
      ],
      "FileCacheOptions": {
        "TtlSeconds": 10,
        "Region": "OcelotCache",
        "Type": "Memory"
      }
    }
  ],
  "GlobalConfiguration": {
    "BaseUrl": "https://localhost:5000",
    "RequestIdKey": "OcRequestId"
  }
}

```

## Configurando o middleware do cache do Ocelot

Agora voltamos no arquivo  Startup.cs onde precisaremos adicionar no método ConfigureServices() para configurar o middleware de cache do Ocelot usando a biblioteca de gerenciamento de cache do CacheManager. Aqui, estamos usando o cache do tipo Dictionary. Você pode usar outros tipos de cache disponíveis na biblioteca, como Redis, Memcached, entre outros.

```
services.AddCacheManager(x =>
{
    x.WithMicrosoftLogging(log =>
    {
        log.AddConsole(LogLevel.Debug);
    })
    .WithJsonSerializer()
    .WithMicrosoftMemoryCacheHandle("MemoryCache");
});
services.AddOcelot().AddCacheManager(option =>
{
    option.WithDictionaryHandle();
});

```