---
title: Informacje o błędzie dotyczące sprawdzania kondycji — Azure Container Registry
description: Kody błędów i możliwe rozwiązania problemów odnalezionych przez uruchomienie polecenia AZ ACR Check-Health Diagnostic w Azure Container Registry
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: 672d446fa8dc27612c7b046cac109bfa4ca5fec5
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309370"
---
# <a name="health-check-error-reference"></a>Informacje o błędach sprawdzania kondycji

Poniżej znajdują się szczegółowe informacje o kodach błędów zwracanych przez polecenie [AZ ACR Check-Health][az-acr-check-health] . Dla każdego błędu są wyświetlane możliwe rozwiązania.

## <a name="dockercommanderror"></a>DOCKER_COMMAND_ERROR

Ten błąd oznacza, że nie można odnaleźć klienta platformy Docker dla interfejsu wiersza polecenia. W związku z tym następujące dodatkowe kontrole nie są uruchamiane: znajdowania wersji platformy Docker, oceniania stanu demona platformy Docker i uruchamiania polecenia Docker pull.

*Potencjalne rozwiązania*: Zainstaluj klienta platformy Docker; Dodaj ścieżkę Docker do zmiennych systemowych.

## <a name="dockerdaemonerror"></a>DOCKER_DAEMON_ERROR

Ten błąd oznacza, że stan demona platformy Docker jest niedostępny lub nie można go osiągnąć przy użyciu interfejsu wiersza polecenia. W efekcie operacje platformy Docker (takie jak `docker login` i `docker pull`) są niedostępne za pomocą interfejsu wiersza polecenia.

*Potencjalne rozwiązania*: Uruchom ponownie demona platformy Docker lub sprawdź, czy jest ona prawidłowo zainstalowana.

## <a name="dockerversionerror"></a>DOCKER_VERSION_ERROR

Ten błąd oznacza, że interfejs wiersza polecenia nie był w stanie `docker --version`uruchomić polecenie.

*Potencjalne rozwiązania*: Spróbuj ręcznie uruchomić polecenie, upewnij się, że masz najnowszą wersję interfejsu wiersza polecenia, i Sprawdź komunikat o błędzie.

## <a name="dockerpullerror"></a>DOCKER_PULL_ERROR

Ten błąd oznacza, że interfejs wiersza polecenia nie może ściągnąć przykładowego obrazu do środowiska.

*Potencjalne rozwiązania*: Sprawdź, czy wszystkie składniki niezbędne do ściągnięcia obrazu działają prawidłowo.

## <a name="helmcommanderror"></a>HELM_COMMAND_ERROR

Ten błąd oznacza, że nie można odnaleźć klienta Helm za pomocą interfejsu wiersza polecenia, co uniemożliwia wykonywanie innych operacji Helm.

*Potencjalne rozwiązania*: Sprawdź, czy zainstalowano klienta Helm i czy jego ścieżka jest dodawana do systemowych zmiennych środowiskowych.

## <a name="helmversionerror"></a>HELM_VERSION_ERROR

Ten błąd oznacza, że interfejs wiersza polecenia nie może określić zainstalowanej wersji programu Helm. Taka sytuacja może wystąpić, jeśli używana wersja interfejsu wiersza polecenia platformy Azure (lub wersja Helm) jest przestarzała.

*Potencjalne rozwiązania*: Zaktualizuj do najnowszej wersji interfejsu wiersza polecenia platformy Azure lub zalecanej wersji Helm; Uruchom polecenie ręcznie i Sprawdź komunikat o błędzie.

## <a name="connectivitydnserror"></a>CONNECTIVITY_DNS_ERROR

Ten błąd oznacza, że serwer DNS dla danego serwera logowania rejestru został wysłany za pomocą polecenia ping, ale nie odpowiedział, co oznacza, że jest niedostępny. Może to wskazywać na problemy z łącznością. Alternatywnie rejestr może nie istnieć, użytkownik może nie mieć uprawnień do rejestru (w celu poprawnego pobrania serwera logowania) lub rejestr docelowy znajduje się w innej chmurze niż używany w interfejsie wiersza polecenia platformy Azure.

*Potencjalne rozwiązania*: Weryfikowanie łączności; Sprawdź pisownię rejestru i czy rejestr istnieje; Sprawdź, czy użytkownik ma odpowiednie uprawnienia i czy chmura rejestru jest taka sama, która jest używana w interfejsie wiersza polecenia platformy Azure.

## <a name="connectivityforbiddenerror"></a>CONNECTIVITY_FORBIDDEN_ERROR

Ten błąd oznacza, że punkt końcowy wyzwania dla danego rejestru odpowiedział z niedozwolonym stanem HTTP 403. Ten błąd oznacza, że użytkownicy nie mają dostępu do rejestru, najprawdopodobniej ze względu na konfigurację sieci wirtualnej.

*Potencjalne rozwiązania*: Usuń reguły sieci wirtualnej lub Dodaj bieżący adres IP klienta do listy dozwolonych.

## <a name="connectivitychallengeerror"></a>CONNECTIVITY_CHALLENGE_ERROR

Ten błąd oznacza, że punkt końcowy wyzwania rejestru docelowego nie wystawia wyzwania.

*Potencjalne rozwiązania*: Spróbuj ponownie za jakiś czas. Jeśli błąd będzie się powtarzać, Otwórz problem pod https://aka.ms/acr/issues adresem.

## <a name="connectivityaadloginerror"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Ten błąd oznacza, że punkt końcowy wyzwania rejestru docelowego wygenerował wyzwanie, ale rejestr nie obsługuje uwierzytelniania Azure Active Directory.

*Potencjalne rozwiązania*: Wypróbuj inny sposób uwierzytelniania, na przykład z poświadczeniami administratora. Jeśli użytkownicy muszą uwierzytelniać się przy użyciu Azure Active Directory, należy otworzyć https://aka.ms/acr/issues problem o.

## <a name="connectivityrefreshtokenerror"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Ten błąd oznacza, że serwer logowania rejestru nie odpowiedział z tokenem odświeżania, więc odmowa dostępu do docelowego rejestru. Ten błąd może wystąpić, jeśli użytkownik nie ma odpowiednich uprawnień do rejestru lub jeśli poświadczenia użytkownika dla interfejsu wiersza polecenia platformy Azure są przestarzałe.

*Potencjalne rozwiązania*: Sprawdź, czy użytkownik ma odpowiednie uprawnienia do rejestru; Uruchom `az login` polecenie, aby odświeżyć uprawnienia, tokeny i poświadczenia.

## <a name="connectivityaccesstokenerror"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Ten błąd oznacza, że serwer logowania do rejestru nie odpowiedział z tokenem dostępu, aby uzyskać dostęp do docelowego rejestru. Ten błąd może wystąpić, jeśli użytkownik nie ma odpowiednich uprawnień do rejestru lub jeśli poświadczenia użytkownika dla interfejsu wiersza polecenia platformy Azure są przestarzałe.

*Potencjalne rozwiązania*: Sprawdź, czy użytkownik ma odpowiednie uprawnienia do rejestru; Uruchom `az login` polecenie, aby odświeżyć uprawnienia, tokeny i poświadczenia.

## <a name="loginservererror"></a>LOGIN_SERVER_ERROR

Ten błąd oznacza, że interfejs wiersza polecenia nie może znaleźć serwera logowania danego rejestru i nie znaleziono domyślnego sufiksu dla bieżącej chmury. Ten błąd może wystąpić, jeśli rejestr nie istnieje, jeśli użytkownik nie ma odpowiednich uprawnień do rejestru, jeśli chmura rejestru i bieżąca chmura interfejsu wiersza polecenia platformy Azure nie są zgodne lub jeśli wersja interfejsu wiersza polecenia platformy Azure jest przestarzała.

*Potencjalne rozwiązania*: Sprawdź, czy pisownia jest poprawna i czy rejestr istnieje; Upewnij się, że użytkownik ma odpowiednie uprawnienia do rejestru oraz że chmury rejestru i środowiska interfejsu wiersza polecenia pasują do siebie. Zaktualizuj interfejs wiersza polecenia platformy Azure do najnowszej wersji.

## <a name="next-steps"></a>Kolejne kroki

Aby sprawdzić kondycję rejestru, zobacz [Sprawdzanie kondycji usługi Azure Container Registry](container-registry-check-health.md).

Zapoznaj się z [często](container-registry-faq.md) zadawanymi pytaniami i innymi znanymi problemami dotyczącymi Azure Container Registry.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
