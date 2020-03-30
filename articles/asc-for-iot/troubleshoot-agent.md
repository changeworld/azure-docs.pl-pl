---
title: Przewodnik po rozwiązywaniu problemów z usługą Azure Security Center for IoT Linux security agent| Dokumenty firmy Microsoft
description: Rozwiązywanie problemów z pracą z usługą Azure Security Center dla agentów zabezpieczeń IoT dla systemu Linux.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: mlottner
ms.openlocfilehash: 7f3bd4be3ef927f73643146a457bc551ef86a450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68600568"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>Przewodnik rozwiązywania problemów z agentem zabezpieczeń (Linux)

W tym artykule wyjaśniono, jak rozwiązać potencjalne problemy w procesie uruchamiania agenta zabezpieczeń.

Usługa Azure Security Center dla agenta IoT uruchamia się samodzielnie natychmiast po instalacji. Proces uruchamiania agenta obejmuje odczytywanie konfiguracji lokalnej, łączenie się z usługą Azure IoT Hub i pobieranie zdalnej konfiguracji bliźniaczej reprezentacji. Niepowodzenie w jednym z tych kroków może spowodować niepowodzenie agenta zabezpieczeń.

W tym przewodniku rozwiązywania problemów dowiesz się, jak:
> [!div class="checklist"]
> * Sprawdzanie poprawności, czy agent zabezpieczeń jest uruchomiony
> * Uzyskaj błędy agenta zabezpieczeń
> * Opis i korygowanie błędów agenta zabezpieczeń 

## <a name="validate-if-the-security-agent-is-running"></a>Sprawdzanie poprawności, czy agent zabezpieczeń jest uruchomiony

1. Aby sprawdzić poprawność jest agent zabezpieczeń jest uruchomiony, poczekaj kilka minut po zainstalowaniu agenta i uruchom następujące polecenie. 
     <br>

    **Agent C**
    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```
    **Agent języka C#**
    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```
2. Jeśli polecenie zwraca pusty wiersz, agent zabezpieczeń nie może uruchomić pomyślnie.    

## <a name="force-stop-the-security-agent"></a>Wymusz zatrzymanie agenta zabezpieczeń 
W przypadkach, gdy agent zabezpieczeń nie może uruchomić, zatrzymaj agenta za pomocą następującego polecenia, a następnie przejdź do poniższej tabeli błędów:

```bash
systemctl stop ASCIoTAgent.service
```
## <a name="get-security-agent-errors"></a>Uzyskaj błędy agenta zabezpieczeń
1. Pobierz błędy agenta zabezpieczeń, uruchamiając następujące polecenie:
    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```
2. Polecenie get security agent error pobiera wszystkie dzienniki utworzone przez agenta Usługi Azure Security Center dla IoT. Poniższa tabela służy do zrozumienia błędów i podjęcia poprawnych kroków w celu skorygowania. 

> [!Note]
> Dzienniki błędów są wyświetlane w porządku chronologicznym. Pamiętaj, aby zanotować sygnaturę czasą każdego błędu, aby pomóc w korygcie. 

## <a name="restart-the-agent"></a>Ponowne uruchamianie agenta

1. Po zlokalizowaniu i naprawieniu błędu agenta zabezpieczeń spróbuj ponownie uruchomić agenta, uruchamiając następujące polecenie. 
    ```bash
    systemctl restart ASCIoTAgent.service
    ```
1. Powtórz poprzedni proces, aby pobrać zatrzymać i pobrać błędy, jeśli agent nadal nie zakończy się niepowodzeniem procesu uruchamiania. 

## <a name="understand-security-agent-errors"></a>Opis błędów agenta zabezpieczeń

Większość błędów agenta zabezpieczeń jest wyświetlana w następującym formacie: 
```
Azure Security Center for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```
| Kod błędu | Kod podrzędny błędu | Szczegóły błędu | Korygowanie C | Korygowanie C # |
|:-----------|:---------------|:--------|:------------|:------------|
| Konfiguracja lokalna | Brak konfiguracji | W lokalnym pliku konfiguracyjnym brakuje konfiguracji. Komunikat o błędzie powinien określać, którego klucza brakuje. | Dodaj brakujący klucz do pliku /var/LocalConfiguration.json, aby uzyskać szczegółowe informacje, zobacz [cs-localconfig-reference.](azure-iot-security-local-configuration-c.md)| Dodaj brakujący klucz do pliku General.config, zobacz [c#-localconfig-reference, aby](azure-iot-security-local-configuration-csharp.md) uzyskać szczegółowe informacje. |
| Konfiguracja lokalna | Konfiguracja analizy przechyłki | Nie można przeanalizować wartości konfiguracji. Komunikat o błędzie powinien określać, którego klucza nie można przeanalizować. Wartość konfiguracji nie może być analizowana, ponieważ wartość nie jest w typie oczekiwanym lub wartość jest poza zakresem. | Napraw wartość klucza w pliku /var/LocalConfiguration.json, aby był zgodny ze schematem LocalConfiguration, zobacz [c#-localconfig-reference, aby](azure-iot-security-local-configuration-csharp.md) uzyskać szczegółowe informacje. |  Napraw wartość klucza w pliku General.config, aby był zgodny ze schematem, zobacz [cs-localconfig-reference, aby](azure-iot-security-local-configuration-c.md) uzyskać szczegółowe informacje.|
| Konfiguracja lokalna | Format pliku | Nie można przeanalizować pliku konfiguracji. | Plik konfiguracyjny jest uszkodzony, pobierz agenta i zainstaluj ponownie. | |
| Konfiguracja zdalna | Limit czasu | Agent nie może pobrać azureiotsecurity moduł twin w okresie limitu czasu. | Upewnij się, że konfiguracja uwierzytelniania jest poprawna i spróbuj ponownie. | Agent nie może pobrać azureiotsecurity moduł twin w okresie limitu czasu. | Upewnij się, że konfiguracja uwierzytelniania jest poprawna i spróbuj ponownie. |
| Uwierzytelnianie | Plik nie istnieje | Plik w danej ścieżce nie istnieje. | Upewnij się, że plik istnieje w danej ścieżce lub przejdź do pliku **LocalConfiguration.json** i zmień konfigurację **Programu FilePath.** | Upewnij się, że plik istnieje w danej ścieżce lub przejdź do pliku **Authentication.config** i zmień konfigurację **filePath.**|
| Uwierzytelnianie | Uprawnienie do pliku | Agent nie ma wystarczających uprawnień do otwierania pliku. | Nadaj **asciotagent** użytkownika odczytać uprawnienia do pliku w danej ścieżce. | Upewnij się, że plik jest dostępny. |
| Uwierzytelnianie | Format pliku | Dany plik nie jest w prawidłowym formacie. | Upewnij się, że plik jest w poprawnym formacie. Obsługiwane typy plików to .pfx i .pem. | Upewnij się, że plik jest prawidłowym plikiem certyfikatu. |
| Uwierzytelnianie | Brak autoryzacji | Agent nie mógł uwierzytelnić się względem usługi IoT Hub z podanymi poświadczeniami. | Sprawdź poprawność konfiguracji uwierzytelniania w pliku LocalConfiguration, przejdź przez konfigurację uwierzytelniania i upewnij się, że wszystkie szczegóły są poprawne, sprawdź, czy klucz tajny w pliku pasuje do uwierzytelnionej tożsamości. | Sprawdź poprawność konfiguracji uwierzytelniania w pliku Authentication.config, przejdź przez konfigurację uwierzytelniania i upewnij się, że wszystkie szczegóły są poprawne, a następnie sprawdź, czy klucz tajny w pliku jest zgodny z uwierzytelnioną tożsamością.
| Uwierzytelnianie | Nie znaleziono | Urządzenie / moduł został znaleziony. | Sprawdź poprawność konfiguracji uwierzytelniania — upewnij się, że nazwa hosta jest poprawna, urządzenie istnieje w centrum IoT Hub i ma moduł bliźniaczej reprezentacji azureiotsecurity. |  Sprawdź poprawność konfiguracji uwierzytelniania — upewnij się, że nazwa hosta jest poprawna, urządzenie istnieje w centrum IoT Hub i ma moduł bliźniaczej reprezentacji azureiotsecurity. |
| Uwierzytelnianie | Brak konfiguracji | W pliku *Authentication.config* brakuje konfiguracji. Komunikat o błędzie powinien określać, którego klucza brakuje. | Dodaj brakujący klucz do pliku *LocalConfiguration.json.*| Dodaj brakujący klucz do pliku *Authentication.config,* aby uzyskać szczegółowe informacje, zobacz [c#-localconfig-reference.](azure-iot-security-local-configuration-csharp.md) |
| Uwierzytelnianie | Konfiguracja analizy przechyłki | Nie można przeanalizować wartości konfiguracji. Komunikat o błędzie powinien określać, którego klucza nie można przeanalizować. Nie można przeanalizować wartości konfiguracji, ponieważ wartość nie jest typu oczekiwanego lub wartość jest poza zakresem. |Napraw wartość klucza w pliku **LocalConfiguration.json.** |Utrwalić wartość klucza w **pliku Authentication.config,** aby dopasować schemat, zobacz [cs-localconfig-reference, aby](azure-iot-security-local-configuration-c.md) uzyskać szczegółowe informacje.|
|

## <a name="restart-the-agent"></a>Ponowne uruchamianie agenta
1. Po zlokalizowaniu i naprawieniu błędu agenta zabezpieczeń uruchom ponownie agenta, uruchamiając następujące polecenie:

    ```bash
    systemctl restart ASCIoTAgent.service
    ```
2. W razie potrzeby powtórz poprzednie procesy, aby wymusić zatrzymanie agenta i pobrać błędy, jeśli agent nadal kończy niepowodzenie procesu uruchamiania. 

## <a name="next-steps"></a>Następne kroki
- Przeczytaj [omówienie](overview.md) usługi Azure Security Center dla IoT
- Dowiedz się więcej o usłudze Azure Security Center dla [architektury](architecture.md) IoT
- Włączanie usługi Azure Security Center dla [usługi](quickstart-onboard-iot-hub.md) IoT
- Przeczytaj często zadawane pytania dotyczące usługi Azure Security Center for [IoT](resources-frequently-asked-questions.md)
- Dowiedz się, jak uzyskać dostęp do [nieprzetworzonych danych zabezpieczeń](how-to-security-data-access.md)
- Zrozumienie [rekomendacji](concept-recommendations.md)
- Opis [alertów](concept-security-alerts.md) zabezpieczeń
