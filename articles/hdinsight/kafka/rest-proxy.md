---
title: Apache Kafka REST proxy — Azure HDInsight
description: Dowiedz się, jak wykonywać operacje apache platformy Kafka przy użyciu serwera proxy Odwościenia platformy Kafka w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: d99a3b803b80dc41990a63e647d3ba928deb31af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198909"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interakcja z klastrami platformy Apache w usłudze Azure HDInsight przy użyciu serwera proxy REST

Serwer proxy Kafka REST umożliwia interakcję z klastrem platformy Kafka za pośrednictwem interfejsu API REST za pośrednictwem protokołu HTTP. Oznacza to, że klienci platformy Kafka mogą znajdować się poza siecią wirtualną. Ponadto klienci mogą wykonywać proste wywołania HTTP do wysyłania i odbierania wiadomości do klastra platformy Kafka, zamiast polegać na bibliotekach platformy Kafka. W tym samouczku pokazano, jak utworzyć klaster platformy Kafka z włączonym serwerem proxy REST i podać przykładowy kod, który pokazuje, jak nawiązywać połączenia z serwerem proxy REST.

## <a name="rest-api-reference"></a>Informacje o interfejsie API REST

Aby uzyskać pełną specyfikację operacji obsługiwanych przez interfejs API OdPR platformy Kafka, zobacz [informacje dotyczące interfejsu API serwera REST HDInsight .](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)

## <a name="background"></a>Tło

![Architektura serwera proxy Kafka REST](./media/rest-proxy/rest-proxy-architecture.png)

Aby uzyskać pełną specyfikację operacji obsługiwanych przez API, zobacz [Apache Kafka REST Proxy API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>Punkt końcowy serwera proxy REST

Utworzenie klastra platformy Windows usługi HDInsight za pomocą serwera proxy REST tworzy nowy publiczny punkt końcowy dla klastra, który można znaleźć w klastrze USŁUGI HDInsight "Właściwości" w witrynie Azure portal.

### <a name="security"></a>Zabezpieczenia

Dostęp do serwera proxy Usługi Kafka REST jest zarządzany za pomocą grup zabezpieczeń usługi Azure Active Directory. Podczas tworzenia klastra platformy Kafka z włączonym serwerem proxy REST należy udostępnić grupę zabezpieczeń usługi Azure Active Directory, która powinna mieć dostęp do punktu końcowego REST. Klienci platformy Kafka (aplikacje), które potrzebują dostępu do serwera proxy REST, powinni być zarejestrowani w tej grupie przez właściciela grupy. Właściciel grupy może to zrobić za pośrednictwem portalu lub za pośrednictwem programu Powershell.

Przed złożeniem żądań do punktu końcowego serwera proxy REST aplikacja kliencka powinna uzyskać token OAuth, aby zweryfikować członkostwo w odpowiedniej grupie zabezpieczeń. Znajdź [przykład aplikacji klienta](#client-application-sample) poniżej, który pokazuje, jak uzyskać token OAuth. Gdy aplikacja kliencka ma token OAuth, muszą przekazać ten token w żądaniu HTTP do serwera proxy REST.

> [!NOTE]  
> Aby dowiedzieć się więcej o grupach zabezpieczeń usługi AAD, zobacz [Zarządzanie dostępem do aplikacji i zasobów przy użyciu grup usługi Azure Active Directory.](../../active-directory/fundamentals/active-directory-manage-groups.md) Aby uzyskać więcej informacji na temat działania tokenów OAuth, zobacz [Autoryzowanie dostępu do aplikacji sieci Web usługi Azure Active Directory przy użyciu przepływu dotacji kodu OAuth 2.0](../../active-directory/develop/v1-protocols-oauth-code.md).

## <a name="prerequisites"></a>Wymagania wstępne

1. Zarejestrować aplikację w usłudze Azure AD. Aplikacje klienckie, które piszesz do interakcji z serwerem proxy Rest platformy Kafka użyje identyfikatora tej aplikacji i klucz tajny do uwierzytelniania na platformie Azure.
1. Utwórz grupę zabezpieczeń usługi Azure AD i dodaj aplikację zarejestrowaną w usłudze Azure AD do grupy zabezpieczeń. Ta grupa zabezpieczeń będzie używana do kontrolowania, które aplikacje mogą wchodzić w interakcje z serwerem proxy REST. Aby uzyskać więcej informacji na temat tworzenia grup usługi Azure AD, zobacz [Tworzenie grupy podstawowej i dodawanie członków przy użyciu usługi Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Tworzenie klastra platformy Kafka z włączonym serwerem proxy REST

1. Podczas tworzenia przepływu pracy klastra platformy Kafka na karcie "Zabezpieczenia + sieć" zaznacz opcję "Włącz serwer proxy Odcół kafka".

     ![Włączanie serwera proxy Usługi Kafka REST i wybieranie grupy zabezpieczeń](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Kliknij **pozycję Wybierz grupę zabezpieczeń**. Z listy grup zabezpieczeń wybierz grupę zabezpieczeń, do której chcesz mieć dostęp do serwera proxy REST. Za pomocą pola wyszukiwania można znaleźć odpowiednią grupę zabezpieczeń. Kliknij przycisk **Wybierz** u dołu.

     ![Włączanie serwera proxy Usługi Kafka REST i wybieranie grupy zabezpieczeń](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Wykonaj pozostałe kroki, aby utworzyć klaster zgodnie z opisem w [obszarze Tworzenie klastra Apache Kafka w usłudze Azure HDInsight przy użyciu portalu Azure.](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started)

1. Po utworzeniu klastra przejdź do właściwości klastra, aby zarejestrować adres URL serwera proxy Usługi Kafka REST.

     ![wyświetl adres URL serwera proxy REST](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Przykład aplikacji klienckiej

Poniższy kod języka Python służy do interakcji z serwerem proxy REST w klastrze platformy Kafka. Aby użyć przykładu kodu, wykonaj następujące kroki:

1. Zapisz przykładowy kod na komputerze z zainstalowanym pythonem.
1. Zainstaluj wymagane zależności pythona, `pip3 install adal` `pip install msrestazure`wykonując i .
1. Zmodyfikuj sekcję kodu *Skonfiguruj te właściwości* i zaktualizuj następujące właściwości dla swojego środowiska:
    1.  *Identyfikator dzierżawy* — dzierżawa platformy Azure, w której znajduje się twoja subskrypcja.
    1.  *Identyfikator klienta* — identyfikator aplikacji zarejestrowanej w grupie zabezpieczeń.
    1.  *Klucz tajny klienta* — klucz tajny dla aplikacji zarejestrowanej w grupie zabezpieczeń
    1.  *Kafkarest_endpoint* — pobierz tę wartość z zakładki "właściwości" w przeglądzie klastra, zgodnie z opisem w [sekcji wdrażania](#create-a-kafka-cluster-with-rest-proxy-enabled). Powinien być w następującym formacie –`https://<clustername>-kafkarest.azurehdinsight.net`
3. Z wiersza polecenia wykonaj plik pythona, wykonując`python <filename.py>`

Ten kod wykonuje następujące czynności:

1. Pobiera token OAuth z usługi Azure AD
1. Pokazuje, jak złożyć żądanie do serwera proxy Kafka REST

Aby uzyskać więcej informacji na temat uzyskiwania tokenów OAuth w pythonie, zobacz [Python AuthenticationContext klasy](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). Może pojawić się opóźnienie, podczas gdy tematy, które nie są tworzone lub usuwane za pośrednictwem serwera proxy ODW kafka są odzwierciedlane tam. To opóźnienie jest spowodowane odświeżenie pamięci podręcznej.

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

## <a name="next-steps"></a>Następne kroki

* [Dokumenty referencyjne interfejsu API serwera proxy systemu Kafka REST](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
