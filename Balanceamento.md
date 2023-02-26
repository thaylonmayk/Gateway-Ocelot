# Para usar o balanceamento de carga com o Ocelot

-  Adicione a dependência do pacote Ocelot.LoadBalancer.LoadBalancerCore ao seu projeto
- Configure o Ocelot com a opção de balanceamento de carga


## Adicionando dependência
Abra o Gerenciador de Pacotes NuGet e instale o pacote Ocelot.LoadBalancer.LoadBalancerCore. Isso pode ser feito através do comando Install-Package Ocelot.LoadBalancer.LoadBalancerCore no Console do Gerenciador de Pacotes NuGet ou adicionando a dependência no arquivo csproj.


## Configurando balanceamento
* A seção "LoadBalancerOptions" define as opções de balanceamento de carga para a rota.
* O tipo de balanceamento de carga é definido na opção "Type". Neste exemplo, o tipo de balanceamento de carga é LeastConnection.

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
        },
        {
          "Host": "localhost",
          "Port": 5001
        },
        {
          "Host": "localhost",
          "Port": 5002
        }
      ],
      "LoadBalancerOptions": {
        "Type": "LeastConnection"
      }
    }
  ],
  "GlobalConfiguration": {
    "BaseUrl": "https://localhost:5000",
    "RequestIdKey": "OcRequestId"
  }
}
```

## Configurando o middleware do balanceamento de carga

Agora voltamos no arquivo  Startup.cs onde precisaremos adicionar no método ConfigureServices()


```
services.AddOcelot().AddLoadBalancer();

```

