---
title: Zabezpieczenia i uwierzytelnianie — usługa Azure Event Grid IoT Edge | Dokumenty firmy Microsoft
description: Zabezpieczenia i uwierzytelnianie w siatce zdarzeń w u źródła IoT.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 5dfa17fd702b76e2cfaa7a91066dbc6749c1069e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844517"
---
# <a name="security-and-authentication"></a>Zabezpieczenia i uwierzytelnianie

Zabezpieczenia i uwierzytelnianie to zaawansowana koncepcja, która wymaga najpierw znajomości podstaw siatki zdarzeń. Rozpocznij [tutaj,](concepts.md) jeśli jesteś nowy w uiszczającej siatkę zdarzeń na przeglądarce IoT Edge. Moduł event grid opiera się na istniejącej infrastrukturze zabezpieczeń w ubudowie IoT. Szczegółowe informacje i konfigurację można znaleźć w [tej dokumentacji.](../../iot-edge/security.md)

W poniższych sekcjach opisano szczegółowo sposób zabezpieczania i uwierzytelniania tych ustawień:

* Konfiguracja protokołu TLS
* Uwierzytelnianie klienta przychodzącego
* Uwierzytelnianie serwera wychodzącego
* Uwierzytelnianie klienta wychodzącego

>[!IMPORTANT]
>Zabezpieczenia modułu event grid i dźwigni uwierzytelniania to istniejąca infrastruktura dostępna w u źródła IoT. Założenie jest takie, że podsystem IoT Edge jest bezpieczny.

>[!IMPORTANT]
>Konfiguracja siatki zdarzeń jest **domyślnie bezpieczna**. Poniższe podsekcje wyjaśniają wszystkie opcje i możliwe wartości, których można użyć do zastąpienia aspektów uwierzytelniania. Przed wprowadzeniem jakichkolwiek zmian zapoznaj się z ich wpływem. Aby zmiany zostały wprowadzone, moduł siatki zdarzeń będzie musiał zostać ponownie rozmieszczony.

## <a name="tls-configuration-aka-server-authentication"></a>Konfiguracja protokołu TLS (uwierzytelnianie serwera również)

Moduł Event Grid obsługuje zarówno punkty końcowe HTTP, jak i HTTPS. Każdy moduł usługi IoT Edge jest przypisywany certyfikatowi serwera przez demon zabezpieczeń usługi IoT Edge. Używamy certyfikatu serwera do zabezpieczenia punktu końcowego. Po wygaśnięciu moduł automatycznie odświeża się przy nowym certyfikacie z demona zabezpieczeń IoT Edge.

Domyślnie dozwolona jest tylko komunikacja HTTPS. To zachowanie można zastąpić za pomocą **inbound__serverAuth__tlsPolicy** konfiguracji. W poniższej tabeli przechwytuje możliwe wartości tej właściwości.

| Możliwe wartości | Opis |
| ---------------- | ------------ |
| Dokładny | Domyślne. Włącza tylko protokół HTTPS
| Enabled (Włączony) | Włącza zarówno protokół HTTP, jak i HTTPS
| Disabled (Wyłączony) | Włącza tylko protokół HTTP

## <a name="inbound-client-authentication"></a>Uwierzytelnianie klienta przychodzącego

Klienci to jednostki wykonujące operacje zarządzania i/lub środowiska uruchomieniowego. Klientami mogą być inne moduły IoT Edge, aplikacje inne niż IoT.

Moduł Event Grid obsługuje dwa typy uwierzytelniania klienta:

* Oparty na kluczu sygnatura dostępu współdzielonego (SAS)
* oparte na certyfikatach

Domyślnie moduł Siatki zdarzeń jest skonfigurowany do akceptowania tylko uwierzytelniania opartego na certyfikatach. Podczas uruchamiania moduł usługi Event Grid pobiera moduł "TrustBundle" z demona zabezpieczeń usługi IoT Edge i używa go do sprawdzania poprawności dowolnego certyfikatu klienta. Certyfikaty klientów, które nie zostaną rozpoznane w tym łańcuchu, zostaną odrzucone za pomocą programu `UnAuthorized`.

### <a name="certificate-based-client-authentication"></a>Uwierzytelnianie klienta oparte na certyfikatach

Uwierzytelnianie oparte na certyfikatach jest domyślnie włączone. Można wyłączyć uwierzytelnianie oparte na certyfikatach za pomocą **inbound__clientAuth__clientCert__enabled**właściwości . W poniższej tabeli przechwytuje się możliwe wartości.

| Możliwe wartości | Opis |
| ----------------  | ------------ |
| true | Domyślne. Wymaga wszystkich żądań do modułu Event Grid do przedstawienia certyfikatu klienta. Ponadto należy skonfigurować **inbound__clientAuth__clientCert__source**.
| false | Nie zmuszaj klienta do przedstawienia certyfikatu.

W poniższej tabeli przechwytuje się możliwe wartości dla **inbound__clientAuth__clientCert__source**

| Możliwe wartości | Opis |
| ---------------- | ------------ |
| IoT Edge | Domyślne. Używa trustbundle usługi IoT Edge do sprawdzania poprawności wszystkich certyfikatów klientów.

Jeśli klient przedstawia podpisy samopodstawowy, domyślnie moduł siatki zdarzeń odrzuci takie żądania. Można zezwolić na certyfikaty klienta z podpisem własnym za pośrednictwem **inbound__clientAuth__clientCert__allowUnknownCA** właściwości. W poniższej tabeli przechwytuje się możliwe wartości.

| Możliwe wartości | Opis |
| ----------------  | ------------|
| true | Domyślne. Umożliwia pomyślne prezentowane certyfikaty z podpisem własnym.
| false | Spowoduje to niepowodzenie żądań, jeśli zostaną przedstawione certyfikaty z podpisem własnym.

>[!IMPORTANT]
>W scenariuszach produkcyjnych można ustawić **inbound__clientAuth__clientCert__allowUnknownCA** na **false**.

### <a name="sas-key-based-client-authentication"></a>Uwierzytelnianie klienta oparte na kluczach SAS

Oprócz uwierzytelniania opartego na certyfikatach moduł siatki zdarzeń może również wykonywać uwierzytelnianie oparte na kluczu SAS. Klucz SAS jest jak klucz tajny skonfigurowany w module siatki zdarzeń, który powinien być używany do sprawdzania poprawności wszystkich połączeń przychodzących. Klienci muszą określić klucz tajny w nagłówku HTTP "aeg-sas-key". Żądanie zostanie `UnAuthorized` odrzucone, jeśli nie będzie zgodne.

Konfiguracja do kontrolowania uwierzytelniania opartego na kluczu SAS jest **inbound__clientAuth__sasKeys__enabled**.

| Możliwe wartości | Opis  |
| ----------------  | ------------ |
| true | Umożliwia uwierzytelnianie oparte na kluczu SAS. Wymaga **inbound__clientAuth__sasKeys__key1** lub **inbound__clientAuth__sasKeys__key2**
| false | Domyślne. Uwierzytelnianie oparte na kluczu SAS jest wyłączone.

 **inbound__clientAuth__sasKeys__key1** i **inbound__clientAuth__sasKeys__key2** są kluczami, które konfigurują moduł siatki zdarzeń w celu sprawdzenia żądań przychodzących. Należy skonfigurować co najmniej jeden z kluczy. Klient składający żądanie będzie musiał przedstawić klucz jako część nagłówka żądania '**aeg-sas-key**'. Jeśli oba klucze są skonfigurowane, klient może przedstawić jeden z kluczy.

> [!NOTE]
>Można skonfigurować obie metody uwierzytelniania. W takim przypadku klucz sygnatury dostępu Współdzielonego jest sprawdzany jako pierwszy i tylko w przypadku niepowodzenia, wykonywane jest uwierzytelnianie oparte na certyfikacie. Aby żądanie zakończyło się pomyślnie, tylko jedna z metod uwierzytelniania musi zakończyć się pomyślnie.

## <a name="outbound-client-authentication"></a>Uwierzytelnianie klienta wychodzącego

Klient w kontekście wychodzącym odnosi się do modułu siatki zdarzeń. Operacja jest dostarczana zdarzeń do subskrybentów. Moduły subskrybujące są traktowane jako serwer.

Każdy moduł usługi IoT Edge jest przypisywany certyfikat tożsamości przez demona zabezpieczeń usługi IoT Edge. Używamy certyfikatu tożsamości dla połączeń wychodzących. Po wygaśnięciu moduł automatycznie odświeża się przy nowym certyfikacie z demona zabezpieczeń IoT Edge.

Konfiguracja do kontrolowania uwierzytelniania klienta wychodzącego jest **outbound__clientAuth__clientCert__enabled**.

| Możliwe wartości | Opis |
| ----------------  | ------------ |
| true | Domyślne. Wymaga, aby wszystkie wychodzące żądania z modułu Event Grid były prezentujące certyfikat. Musi skonfigurować **outbound__clientAuth__clientCert__source**.
| false | Nie wymagaj modułu Event Grid do przedstawienia swojego certyfikatu.

Konfiguracja, która steruje źródłem certyfikatu, jest **outbound__clientAuth__clientCert__source**.

| Możliwe wartości | Opis |
| ---------------- | ------------ |
| IoT Edge | Domyślne. Używa certyfikatu tożsamości modułu skonfigurowany przez demona zabezpieczeń usługi IoT Edge.

### <a name="outbound-server-authentication"></a>Uwierzytelnianie serwera wychodzącego

Jednym z typów docelowych dla subskrybenta usługi Event Grid jest "Webhook". Domyślnie dla takich subskrybentów akceptowane są tylko punkty końcowe HTTPS.

Konfiguracja do sterowania outbound__webhook__httpsOnly **zasad**docelowych elementu webhook .

| Możliwe wartości | Opis |
| ----------------  | ------------ |
| true | Domyślne. Zezwala tylko subskrybentom z punktem końcowym HTTPS.
| false | Zezwala subskrybentom z punktem końcowym HTTP lub HTTPS.

Domyślnie moduł usługi Event Grid sprawdza poprawność certyfikatu serwera subskrybenta. Sprawdzanie poprawności można pominąć, zastępując **outbound__webhook__skipServerCertValidation**. Możliwe wartości:

| Możliwe wartości | Opis |
| ----------------  | ------------ |
| true | Nie sprawdzaj poprawności certyfikatu serwera subskrybenta.
| false | Domyślne. Sprawdź poprawność certyfikatu serwera subskrybenta.

Jeśli certyfikat subskrybenta jest podpisany samodzielnie, moduł domyślnie usługa Event Grid odrzuci takich subskrybentów. Aby zezwolić na certyfikat z podpisem własnym, można zastąpić **outbound__webhook__allowUnknownCA**. W poniższej tabeli przedstawiono możliwe wartości.

| Możliwe wartości | Opis |
| ----------------  | ------------ |
| true | Domyślne. Umożliwia pomyślne prezentowane certyfikaty z podpisem własnym.
| false | Spowoduje to niepowodzenie żądań, jeśli zostaną przedstawione certyfikaty z podpisem własnym.

>[!IMPORTANT]
>W scenariuszach produkcyjnych należy ustawić **outbound__webhook__allowUnknownCA** **na false**.

> [!NOTE]
>Środowisko IoT Edge generuje certyfikaty z podpisem własnym. Zaleca się generowanie certyfikatów wystawionych przez autoryzowane ca dla obciążeń produkcyjnych i ustawienie **właściwość allowUnknownCA** zarówno dla przychodzącego, jak i wychodzącego do **false.**

## <a name="summary"></a>Podsumowanie

Moduł siatki zdarzeń jest **domyślnie bezpieczny**. Zaleca się zachowanie tych wartości domyślnych dla wdrożeń produkcyjnych.

Poniżej przedstawiono zasady przewodnie, których należy używać podczas konfigurowania:

* Zezwalaj tylko na żądania HTTPS do modułu.
* Zezwalaj tylko na uwierzytelnianie klienta oparte na certyfikatach. Zezwalaj tylko na te certyfikaty, które są wystawiane przez dobrze znane certyfikaty. Nie zezwalaj na certyfikaty z podpisem własnym.
* Nie zezwalaj na uwierzytelnianie klienta oparte na saskey.
* Zawsze prezentuj certyfikat tożsamości modułu Event Grid w przypadku połączeń wychodzących.
* Zezwalaj tylko subskrybentom HTTPS na typy docelowe elementu Webhook.
* Zawsze sprawdzaj poprawność certyfikatu serwera subskrybenta dla typów docelowych elementu Webhook. Zezwalaj tylko na certyfikaty wystawione przez dobrze znane certyfikaty. Nie zezwalaj na certyfikaty z podpisem własnym.

Domyślnie moduł Siatki zdarzeń jest wdrażany z następującą konfiguracją:

 ```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
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
