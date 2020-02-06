---
title: Apache Kafka proxy REST — Azure HDInsight
description: Dowiedz się, jak wykonywać Apache Kafka operacje przy użyciu serwera proxy REST Kafka w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: b53fc3af71ce872c9ca9f513139c8179fd4165ed
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031411"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Korzystanie z klastrów Apache Kafka w usłudze Azure HDInsight przy użyciu serwera proxy REST

Serwer proxy REST Kafka umożliwia współdziałanie z klastrem Kafka za pośrednictwem interfejsu API REST za pośrednictwem protokołu HTTP. Oznacza to, że klienci Kafka mogą znajdować się poza siecią wirtualną. Ponadto klienci mogą tworzyć proste wywołania HTTP do wysyłania i odbierania komunikatów do klastra Kafka, zamiast polegać na bibliotekach Kafka.  

## <a name="background"></a>Tło

### <a name="architecture"></a>Architektura

Bez serwera proxy REST Kafka klienci muszą znajdować się w tej samej sieci wirtualnej co klaster Kafka lub w równorzędnej sieci wirtualnej. Serwer proxy REST umożliwia łączenie się z producentami danych lub konsumentami znajdującymi się w dowolnym miejscu. Wdrożenie serwera proxy REST tworzy nowy Publiczny punkt końcowy dla klastra, który można znaleźć w ustawieniach portalu.

Pełną specyfikację operacji obsługiwanych przez interfejs API można znaleźć w temacie [Apache KAFKA API REST proxy](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="security"></a>Bezpieczeństwo

Dostęp do serwera proxy REST Kafka jest zarządzany przy użyciu grup zabezpieczeń Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem do aplikacji i zasobów przy użyciu grup Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups).

Podczas tworzenia klastra Kafka z włączonym serwerem proxy REST zostanie podaną grupę zabezpieczeń usługi AAD, która powinna mieć dostęp do punktu końcowego REST. Klienci Kafka (aplikacje), którzy muszą mieć dostęp do serwera proxy REST, powinny być zarejestrowani do tej grupy przez właściciela grupy. Właściciel grupy może to zrobić za pośrednictwem portalu lub za pomocą programu PowerShell.

Przed przekazaniem żądań do punktu końcowego serwera proxy REST aplikacja kliencka powinna uzyskać token OAuth w celu zweryfikowania członkostwa odpowiednich grup zabezpieczeń. Aby uzyskać więcej informacji na temat działania tokenów OAuth, zobacz [Autoryzuj dostęp do aplikacji sieci web Azure Active Directory przy użyciu przepływu przyznawania kodu OAuth 2,0](../../active-directory/develop/v1-protocols-oauth-code.md). Aby zapoznać się z przykładem pobierania tokenu OAuth w języku Python, zobacz [przykład aplikacji klienckiej](#client-application-sample)

Gdy aplikacja kliencka ma token OAuth, musi przekazać ten token w żądaniu HTTP wprowadzonym do serwera proxy REST.

## <a name="prerequisites"></a>Wymagania wstępne

1. Zarejestrować aplikację w usłudze Azure AD. Aplikacje klienckie zapisywane w celu współdziałania z serwerem proxy REST Kafka będą używać tego identyfikatora aplikacji i klucza tajnego do uwierzytelniania na platformie Azure.
1. Utwórz grupę zabezpieczeń usługi Azure AD i Dodaj aplikację, która została zarejestrowana w usłudze Azure AD, do grupy zabezpieczeń. Ta grupa zabezpieczeń zostanie użyta do kontrolowania, które aplikacje mogą korzystać z serwera proxy REST. Aby uzyskać więcej informacji na temat tworzenia grup usługi Azure AD, zobacz [Tworzenie grupy podstawowej i Dodawanie członków przy użyciu Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Tworzenie klastra Kafka z włączonym serwerem proxy REST

1. Podczas przepływu pracy tworzenia klastra Kafka na karcie "zabezpieczenia i sieć" zaznacz opcję "Włącz serwer proxy REST Kafka".

     ![Włącz serwer proxy REST Kafka i wybierz grupę zabezpieczeń](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Kliknij pozycję **Wybierz grupę zabezpieczeń**. Z listy grup zabezpieczeń wybierz grupę zabezpieczeń, która ma mieć dostęp do serwera proxy REST. Możesz użyć pola wyszukiwania, aby znaleźć odpowiednią grupę zabezpieczeń. Kliknij przycisk **Wybierz** u dołu.

     ![Włącz serwer proxy REST Kafka i wybierz grupę zabezpieczeń](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Wykonaj pozostałe kroki w celu utworzenia klastra zgodnie z opisem w temacie [Tworzenie klastra Apache Kafka w usłudze Azure HDInsight przy użyciu Azure Portal](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. Po utworzeniu klastra przejdź do właściwości klastra, aby zarejestrować adres URL serwera proxy REST Kafka.

     ![Wyświetl adres URL serwera proxy REST](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Przykład aplikacji klienta

Możesz użyć poniższego kodu w języku Python do współpracy z serwerem proxy REST w klastrze Kafka. Ten kod wykonuje następujące czynności:

1. Pobiera token OAuth z usługi Azure AD
1. Tworzy określony temat
1. Wysyła komunikaty do tego tematu
1. Używa komunikatów z tego tematu

Aby uzyskać więcej informacji na temat uzyskiwania tokenów OAuth w języku Python, zobacz [Python AuthenticationContext Class](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). W tym miejscu mogą być widoczne opóźnienia, które nie zostały utworzone lub usunięte za pomocą serwera proxy REST Kafka. To opóźnienie jest spowodowane odświeżeniem pamięci podręcznej.

```python
#Required python packages
#pip3 install adal
#pip install msrestazure

import adal
from msrestazure.azure_active_directory import AdalAuthentication
from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
import requests

#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

#getting token
login_endpoint = AZURE_PUBLIC_CLOUD.endpoints.active_directory
resource = "https://hib.azurehdinsight.net"
context = adal.AuthenticationContext(login_endpoint + '/' + tenant_id)

token = context.acquire_token_with_client_credentials(
    resource,
    client_id,
    client_secret)

accessToken = 'Bearer ' + token['accessToken']

print(accessToken)

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

Aby użyć przykładu kodu, wykonaj następujące kroki:

1. Zapisz przykładowy kod na komputerze, na którym zainstalowano Język Python.
1. Zainstaluj wymagane zależności języka Python, wykonując `pip3 install adal` i `pip install msrestazure`.
1. Zmodyfikuj kod i zaktualizuj następujące właściwości środowiska:
    1.  *Identyfikator dzierżawy* — dzierżawy platformy Azure, w której znajduje się subskrypcja.
    1.  *Identyfikator klienta* — identyfikator aplikacji zarejestrowanej w grupie zabezpieczeń.
    1.  *Klucz tajny klienta* — wpis tajny dla aplikacji, która została zarejestrowana w grupie zabezpieczeń
    1.  *Kafkarest_endpoint* — Pobierz tę wartość z karty "właściwości" w artykule Omówienie klastra zgodnie z opisem w [sekcji Wdrażanie](#create-a-kafka-cluster-with-rest-proxy-enabled). Powinien mieć następujący format — `https://<clustername>-kafkarest.azurehdinsight.net`
3. W wierszu polecenia wykonaj plik Python, wykonując `python <filename.py>`

## <a name="next-steps"></a>Następne kroki

* [Dokumenty referencyjne interfejsu API REST usługi Kafka](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
* [Samouczek: Używanie interfejsów API producenta i klienta Apache Kafka](apache-kafka-producer-consumer-api.md)