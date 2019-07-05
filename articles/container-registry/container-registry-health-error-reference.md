---
title: Błąd odwołania dla kontroli kondycji — usługa Azure Container Registry
description: Kody błędów i możliwe rozwiązania problemy znalezione za pomocą polecenia az acr kondycja sprawdzania diagnostycznych w usłudze Azure Container Registry
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: fc29b27cbb7eea983140c59529d981ad95c27ae8
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555113"
---
# <a name="health-check-error-reference"></a>Informacje o błędzie sprawdzania kondycji

Poniżej przedstawiono szczegółowe informacje dotyczące kodów błędów zwróconych przez [kondycja sprawdzania az acr][az-acr-check-health] polecenia. Dla każdego błędu są wyświetlane możliwych rozwiązań.

## <a name="dockercommanderror"></a>DOCKER_COMMAND_ERROR

Ten błąd oznacza, że nie można odnaleźć klienta Docker dla interfejsu wiersza polecenia. W rezultacie nie są uruchamiane następujące dodatkowe kontrole: znajdowanie stanu demona wersji platformy Docker, ocena platformy Docker i Docker działa ściągnięcia polecenia.

*Potencjalne rozwiązania*: Zainstaluj klienta Docker; Dodaj ścieżkę platformy Docker do zmiennych systemowych.

## <a name="dockerdaemonerror"></a>DOCKER_DAEMON_ERROR

Ten błąd oznacza, że stanu demona platformy Docker jest niedostępna lub że go nie można osiągnąć przy użyciu interfejsu wiersza polecenia. W rezultacie operacje platformy Docker (takie jak `docker login` i `docker pull`) są niedostępne za pośrednictwem interfejsu wiersza polecenia.

*Potencjalne rozwiązania*: Ponownie uruchom demona platformy Docker lub sprawdzić, czy jest zainstalowany poprawnie.

## <a name="dockerversionerror"></a>DOCKER_VERSION_ERROR

Ten błąd oznacza, że interfejs wiersza polecenia nie był w stanie uruchomić polecenie `docker --version`.

*Potencjalne rozwiązania*: Spróbuj ręcznie uruchomić polecenie, upewnij się, jest zainstalowana najnowsza wersja interfejsu wiersza polecenia i zbadaj komunikat o błędzie.

## <a name="dockerpullerror"></a>DOCKER_PULL_ERROR

Ten błąd oznacza, że interfejsu wiersza polecenia nie był w stanie ściągnąć obraz próbki do środowiska.

*Potencjalne rozwiązania*: Sprawdź, czy wszystkie składniki niezbędne do ściągania obrazu działają prawidłowo.

## <a name="helmcommanderror"></a>HELM_COMMAND_ERROR

Ten błąd oznacza, że nie można odnaleźć tego klienta Helm przez interfejs wiersza polecenia, co wyklucza inne operacje narzędzia Helm.

*Potencjalne rozwiązania*: Sprawdź, czy narzędzia Helm, klient jest zainstalowany i czy jego ścieżki została dodana do zmiennych środowiskowych systemu.

## <a name="helmversionerror"></a>HELM_VERSION_ERROR

Ten błąd oznacza, że interfejs wiersza polecenia nie może ustalić zainstalowaną wersję narzędzia Helm. Może się to zdarzyć, jeśli w wersji wiersza polecenia platformy Azure (lub, jeśli wersja narzędzia Helm) używana jest przestarzały.

*Potencjalne rozwiązania*: Aktualizowanie do najnowszej wersji interfejsu wiersza polecenia platformy Azure lub do zalecanej wersji narzędzia Helm; ręcznie uruchom polecenie i zbadaj komunikat o błędzie.

## <a name="connectivitydnserror"></a>CONNECTIVITY_DNS_ERROR

Ten błąd oznacza, że nazwy DNS dla serwera logowania rejestru danego był za pomocą polecenia ping, ale nie odpowiedziała, co oznacza, że jest ona niedostępna. Może to oznaczać, że niektóre problemy z łącznością. Alternatywnie rejestru nie istnieje, użytkownik może nie mieć uprawnień w rejestrze (Aby prawidłowo pobrać jego serwer logowania) lub rejestrze docelowym znajduje się w innej chmurze niż używane w interfejsie wiersza polecenia platformy Azure.

*Potencjalne rozwiązania*: Sprawdź poprawność połączenia; Sprawdź pisownię rejestru, a istnieje tego rejestru Sprawdź, czy użytkownik ma odpowiednie uprawnienia na nim i chmury w rejestrze jest taki sam, który jest używany w interfejsie wiersza polecenia platformy Azure.

## <a name="connectivityforbiddenerror"></a>CONNECTIVITY_FORBIDDEN_ERROR

Ten błąd oznacza to, czy punkt końcowy wyzwanie dla danego rejestru odpowiedziała, zgłaszając 403 stan HTTP jest zabronione. Ten błąd oznacza, że użytkownicy nie mają dostępu do rejestru, najprawdopodobniej z powodu konfiguracji sieci wirtualnej.

*Potencjalne rozwiązania*: Usuń reguły sieci wirtualnej lub dodać bieżący adres IP klienta do listy dozwolonych.

## <a name="connectivitychallengeerror"></a>CONNECTIVITY_CHALLENGE_ERROR

Ten błąd oznacza, że żądania punktu końcowego rejestru docelowego nie wystawił wyzwanie.

*Potencjalne rozwiązania*: Spróbuj ponownie po pewnym czasie. Jeśli błąd będzie się powtarzać, otwórz problem w https://aka.ms/acr/issues.

## <a name="connectivityaadloginerror"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Ten błąd oznacza, że punkt końcowy wyzwanie rejestru docelowego wystawione żądanie, ale rejestru nie obsługuje uwierzytelniania usługi Azure Active Directory.

*Potencjalne rozwiązania*: Wypróbuj inną metodę uwierzytelniania, na przykład za pomocą poświadczeń administratora. Jeśli użytkownicy muszą przeprowadzić uwierzytelnienie przy użyciu usługi Azure Active Directory, otwórz problem w https://aka.ms/acr/issues.

## <a name="connectivityrefreshtokenerror"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Ten błąd oznacza, że adres serwera logowania rejestru nie odpowiedział przy użyciu tokenu odświeżania, więc nastąpiła odmowa dostępu do rejestru docelowego. Ten błąd może wystąpić, jeśli użytkownik nie ma odpowiednich uprawnień w rejestrze lub poświadczenia użytkownika dla wiersza polecenia platformy Azure są przestarzałe.

*Potencjalne rozwiązania*: Sprawdź, czy użytkownik ma odpowiednie uprawnienia w rejestrze; Uruchom `az login` odświeżania uprawnień, tokenów i poświadczeń.

## <a name="connectivityaccesstokenerror"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Ten błąd oznacza, że adres serwera logowania rejestru nie odpowiedział przy użyciu tokenu dostępu, tak, że nastąpiła odmowa dostępu do rejestru docelowego. Ten błąd może wystąpić, jeśli użytkownik nie ma odpowiednich uprawnień w rejestrze lub poświadczenia użytkownika dla wiersza polecenia platformy Azure są przestarzałe.

*Potencjalne rozwiązania*: Sprawdź, czy użytkownik ma odpowiednie uprawnienia w rejestrze; Uruchom `az login` odświeżania uprawnień, tokenów i poświadczeń.

## <a name="loginservererror"></a>LOGIN_SERVER_ERROR

Ten błąd oznacza, że interfejs wiersza polecenia nie może znaleźć serwera logowania rejestru danego i nie domyślnym sufiksem znaleziono dla bieżącej chmury. Ten błąd może wystąpić, jeśli rejestru nie istnieje, jeśli użytkownik ma odpowiednie uprawnienia w rejestrze, jeśli w rejestrze chmury i bieżącej chmury wiersza polecenia platformy Azure nie są zgodne lub wersji wiersza polecenia platformy Azure jest przestarzały.

*Potencjalne rozwiązania*: Sprawdź, że pisownia jest poprawna i czy istnieje rejestru; Sprawdź ten użytkownik ma odpowiednie uprawnienia w rejestrze, i być zgodne z chmury w rejestrze i środowiska interfejsu wiersza polecenia; Aktualizowanie wiersza polecenia platformy Azure do najnowszej wersji.

## <a name="next-steps"></a>Kolejne kroki

Dla opcji sprawdzić kondycję rejestru, zobacz [sprawdzenia kondycji usługi Azure container registry](container-registry-check-health.md).

Zobacz [— często zadawane pytania](container-registry-faq.md) często zadawane pytania i inne znane problemy dotyczące usługi Azure Container Registry.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
