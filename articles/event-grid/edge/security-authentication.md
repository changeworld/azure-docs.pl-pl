---
title: Zabezpieczenia i uwierzytelnianie — IoT Edge Azure Event Grid | Microsoft Docs
description: Zabezpieczenia i uwierzytelnianie w Event Grid na IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 77b4b265b2e993ccdbc9e07fd2dab5a37ed22a6b
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992160"
---
# <a name="security-and-authentication"></a>Zabezpieczenia i uwierzytelnianie

Bezpieczeństwo i uwierzytelnianie jest zaawansowaną koncepcją i najpierw wymaga znajomości Event Grid podstawowych. Zacznij [tutaj](concepts.md) , jeśli dopiero zaczynasz Event Grid w IoT Edge. Moduł Event Grid kompiluje się w istniejącej infrastrukturze zabezpieczeń na IoT Edge. Zapoznaj się z [tą dokumentacją](../../iot-edge/security.md) , aby uzyskać szczegółowe informacje i konfigurację.

W poniższych sekcjach szczegółowo opisano, jak te ustawienia są zabezpieczone i uwierzytelniane:

* Konfiguracja protokołu TLS
* Uwierzytelnianie klienta przychodzącego
* Uwierzytelnianie serwera wychodzącego
* Uwierzytelnianie klienta wychodzącego

>[!IMPORTANT]
>Event Grid zabezpieczenia i uwierzytelnianie modułu wykorzystują istniejącą infrastrukturę dostępną w IoT Edge. Przyjęto założenie, że podsystem IoT Edge jest bezpieczny.

>[!IMPORTANT]
>Konfiguracja Event Grid jest **Domyślnie zabezpieczona**. W poniższych podsekcjach objaśniono wszystkie opcje i możliwe wartości, których można użyć w celu zastąpienia aspektów uwierzytelniania. Zrozumienie wpływu przed wprowadzeniem jakichkolwiek zmian. Aby zmiany zaczęły obowiązywać, należy ponownie wdrożyć moduł Event Grid.

## <a name="tls-configuration-aka-server-authentication"></a>Konfiguracja protokołu TLS (a. k. uwierzytelnianie serwera)

Moduł Event Grid obsługuje zarówno punkty końcowe HTTP, jak i HTTPS. Każdy moduł IoT Edge ma przypisany certyfikat serwera za pomocą demona zabezpieczeń IoT Edge. Certyfikat serwera jest używany do zabezpieczenia punktu końcowego. Po wygaśnięciu moduł automatycznie odświeża przy użyciu nowego certyfikatu z poziomu demona zabezpieczeń IoT Edge.

Domyślnie dozwolona jest tylko komunikacja przy użyciu protokołu HTTPS. To zachowanie można zastąpić za pomocą protokołu **przychodzącego: obiektu ServerAuth: tlsPolicy** Configuration. W poniższej tabeli przedstawiono możliwe wartości tej właściwości.

| Możliwe wartości | Opis |
| ---------------- | ------------ |
| Ścisłe | Domyślne. Włącza tylko HTTPS
| Enabled (Włączony) | Włącza zarówno protokół HTTP, jak i HTTPS
| Disabled (Wyłączony) | Włącza tylko protokół HTTP

## <a name="inbound-client-authentication"></a>Uwierzytelnianie klienta przychodzącego

Klienci są jednostkami wykonującymi operacje zarządzania i/lub środowiska uruchomieniowego. Klienci mogą być innymi modułami IoT Edge, aplikacjami nieiot.

Moduł Event Grid obsługuje dwa typy uwierzytelniania klientów:-

* Oparty na kluczu sygnatury dostępu współdzielonego (SAS)
* oparte na certyfikatach

Domyślnie moduł Event Grid jest skonfigurowany do akceptowania tylko uwierzytelniania opartego na certyfikatach. Podczas uruchamiania Moduł Event Grid pobiera "TrustBundle" z demona zabezpieczeń IoT Edge i używa go do weryfikacji dowolnego certyfikatu klienta. Certyfikaty klienta, które nie są rozpoznawane w tym łańcuchu, będą odrzucane za pomocą `UnAuthorized`.

### <a name="certificate-based-client-authentication"></a>Uwierzytelnianie klienta oparte na certyfikatach

Uwierzytelnianie oparte na certyfikatach jest domyślnie włączone. Uwierzytelnianie oparte na certyfikatach można wyłączyć za pomocą właściwości **przychodzące: clientAuth: clientCert: Enabled**. W poniższej tabeli przedstawiono możliwe wartości.

| Możliwe wartości | Opis |
| ----------------  | ------------ |
| true | Domyślne. Wymaga, aby wszystkie żądania kierowane do modułu Event Grid zaprezentować certyfikat klienta. Ponadto konieczne będzie skonfigurowanie **ruchu przychodzącego: clientAuth: clientCert: Source**.
| false | Nie Wymuszaj, aby klient mógł przedstawić certyfikat.

W poniższej tabeli przedstawiono możliwe wartości dla **ruchu przychodzącego: clientAuth: clientCert: Source**

| Możliwe wartości | Opis |
| ---------------- | ------------ |
| IoT Edge | Domyślne. Używa Trustbundle IoT Edge do weryfikacji wszystkich certyfikatów klienta.

Jeśli klient przedstawia z podpisem własnym, domyślnie moduł Event Grid będzie odrzucał takie żądania. Możesz zezwolić na certyfikaty klienta z podpisem własnym za pomocą właściwości **przychodzące: clientAuth: clientCert: allowUnknownCA** . W poniższej tabeli przedstawiono możliwe wartości.

| Możliwe wartości | Opis |
| ----------------  | ------------|
| true | Domyślne. Zezwala na pomyślne prezentowanie certyfikatów z podpisem własnym.
| false | Żądania będą kończyć się niepowodzeniem, jeśli są prezentowane certyfikaty z podpisem własnym.

>[!IMPORTANT]
>W scenariuszach produkcyjnych można ustawić wartość **przychodzącą: clientAuth: clientCert: allowUnknownCA** na **false**.

### <a name="sas-key-based-client-authentication"></a>Uwierzytelnianie klienta oparte na kluczach SAS

Oprócz uwierzytelniania opartego na certyfikatach moduł Event Grid może również wykonywać uwierzytelnianie oparte na kluczach SAS. Klucz sygnatury dostępu współdzielonego jest taki sam jak wpis tajny skonfigurowany w module Event Grid, który powinien być używany do weryfikacji wszystkich wywołań przychodzących. Klienci muszą określić klucz tajny w nagłówku HTTP "AEG-SAS-Key". Żądanie zostanie odrzucone z `UnAuthorized`, jeśli nie jest zgodne.

Konfiguracja kontrolowania uwierzytelniania opartego na kluczu SAS jest **przychodząca: clientAuth: sasKeys: Enabled**.

| Możliwe wartości | Opis  |
| ----------------  | ------------ |
| true | Zezwala na uwierzytelnianie oparte na kluczach SAS. Wymaga **ruchu przychodzącego: clientAuth: sasKeys: Klucz1** lub **przychodzącego: clientAuth: sasKeys: klucz2**
| false | Domyślne. Uwierzytelnianie oparte na kluczach SAS jest wyłączone.

 **przychodzące: clientAuth: sasKeys: Klucz1** i **przychodzace: clientAuth: sasKeys: klucz2** to klucze, które konfigurują moduł Event Grid, aby sprawdzać przychodzące żądania. Należy skonfigurować co najmniej jeden klucz. Klient wykonujący żądanie będzie musiał przedstawić klucz jako część nagłówka żądania "**AEG-SAS-Key**". Jeśli oba klucze są skonfigurowane, klient może przedstawić jeden z kluczy.

> [!NOTE]
>Można skonfigurować obie metody uwierzytelniania. W takim przypadku klucz SAS przypadku jest sprawdzany jako pierwszy i tylko wtedy, gdy to się nie powiedzie, zostanie wykonane uwierzytelnianie oparte na certyfikacie. Aby żądanie powiodło się, tylko jedna z metod uwierzytelniania musi się powieść.

## <a name="outbound-client-authentication"></a>Uwierzytelnianie klienta wychodzącego

Klient w kontekście wychodzącym odwołuje się do modułu Event Grid. Wykonywana operacja jest dostarczająca zdarzenia do subskrybentów. Moduły subskrybowania są traktowane jako serwer.

Każdy moduł IoT Edge ma przypisany certyfikat tożsamości przez demona zabezpieczeń IoT Edge. Używamy certyfikatu tożsamości dla wywołań wychodzących. Po wygaśnięciu moduł automatycznie odświeża przy użyciu nowego certyfikatu z poziomu demona zabezpieczeń IoT Edge.

Konfiguracja do kontrolowania uwierzytelniania klienta wychodzącego jest **wychodząca: clientAuth: clientCert: Enabled**.

| Możliwe wartości | Opis |
| ----------------  | ------------ |
| true | Domyślne. Wymaga, aby wszystkie żądania wychodzące z modułu Event Grid zaprezentować certyfikat. Należy skonfigurować **wychodzące: clientAuth: clientCert: Source**.
| false | Nie wymagaj modułu Event Grid do zaprezentowania swojego certyfikatu.

Konfiguracja, która kontroluje Źródło certyfikatu, jest **wychodząca: clientAuth: clientCert: Source**.

| Możliwe wartości | Opis |
| ---------------- | ------------ |
| IoT Edge | Domyślne. Używa certyfikatu tożsamości modułu skonfigurowanego za pomocą demona zabezpieczeń IoT Edge.

### <a name="outbound-server-authentication"></a>Uwierzytelnianie serwera wychodzącego

Jednym z typów docelowych dla subskrybenta Event Grid jest "webhook". Domyślnie tylko punkty końcowe HTTPS są akceptowane dla takich subskrybentów.

Konfiguracja do kontrolowania zasad docelowych elementu webhook **wychodzących: webhook: httpsOnly**.

| Możliwe wartości | Opis |
| ----------------  | ------------ |
| true | Domyślne. Zezwala tylko subskrybentom z punktem końcowym HTTPS.
| false | Zezwala na subskrybenci z punktem końcowym HTTP lub HTTPS.

Domyślnie moduł Event Grid sprawdza poprawność certyfikatu serwera subskrybenta. Możesz pominąć walidację, zastępując **wychodzące: element webhook: skipServerCertValidation**. Możliwe wartości to:-

| Możliwe wartości | Opis |
| ----------------  | ------------ |
| true | Nie Weryfikuj certyfikatu serwera subskrybenta.
| false | Domyślne. Sprawdź poprawność certyfikatu serwera subskrybenta.

Jeśli certyfikat subskrybenta jest z podpisem własnym, domyślnie moduł Event Grid będzie odrzucać takich subskrybentów. Aby zezwolić na certyfikat z podpisem własnym, można przesłonić **wychodzące: element webhook: allowUnknownCA**. W poniższej tabeli przedstawiono możliwe wartości.

| Możliwe wartości | Opis |
| ----------------  | ------------ |
| true | Domyślne. Zezwala na pomyślne prezentowanie certyfikatów z podpisem własnym.
| false | Żądania będą kończyć się niepowodzeniem, jeśli są prezentowane certyfikaty z podpisem własnym.

>[!IMPORTANT]
>W scenariuszach produkcyjnych należy ustawić **wychodzące: element webhook: allowUnknownCA** na **wartość false**.

> [!NOTE]
>Środowisko IoT Edge generuje certyfikaty z podpisem własnym. Zalecenie polega na wygenerowaniu certyfikatów wystawionych przez autoryzowanych urzędów certyfikacji dla obciążeń produkcyjnych i ustawiania właściwości **allowUnknownCA** zarówno dla ruchu przychodzącego, jak i wychodzącego do **Fałsz**.

## <a name="summary"></a>Podsumowanie

Moduł Event Grid jest **zabezpieczony domyślnie**. Zalecamy zachowywanie tych ustawień domyślnych dla wdrożeń produkcyjnych.

Poniżej przedstawiono zasady dotyczące identyfikatorów GUID używanych podczas konfigurowania:-

* Zezwalaj tylko na żądania HTTPS do modułu.
* Zezwalaj tylko na uwierzytelnianie klienta oparte na certyfikatach. Zezwalaj tylko na te certyfikaty, które są wystawiane przez dobrze znane urzędy certyfikacji. Nie Zezwalaj na certyfikaty z podpisem własnym.
* Nie Zezwalaj na uwierzytelnianie klienta oparte na SASKey.
* Zawsze obecny certyfikat tożsamości modułu Event Grid na wywołaniach wychodzących.
* Zezwalaj tylko subskrybentom HTTPS na typy docelowe elementu webhook.
* Zawsze Weryfikuj certyfikat serwera subskrybenta dla typów docelowych elementu webhook. Zezwalaj tylko na certyfikaty wystawiane przez dobrze znane urzędy certyfikacji. Nie Zezwalaj na certyfikaty z podpisem własnym.

Domyślnie moduł Event Grid jest wdrażany z następującą konfiguracją:-

 ```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge",
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true",
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge",
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```
