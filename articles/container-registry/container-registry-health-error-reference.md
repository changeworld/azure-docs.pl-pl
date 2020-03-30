---
title: Odwołanie do błędu dla kontroli kondycji
description: Kody błędów i możliwe rozwiązania problemów znalezionych przez uruchomienie polecenia diagnostycznego az acr check-health w rejestrze kontenerów platformy Azure
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: 971b28b2bf8d9ac22cec0efe979837886762cf17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289145"
---
# <a name="health-check-error-reference"></a>Odwołanie do błędu sprawdzania kondycji

Poniżej przedstawiono szczegółowe informacje o kodach błędów zwracanych przez polecenie [az acr check-health.][az-acr-check-health] Dla każdego błędu są wyświetlane możliwe rozwiązania.

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

Ten błąd oznacza, że nie można odnaleźć klienta platformy Docker dla interfejsu wiersza polecenia. W rezultacie nie są uruchamiane następujące dodatkowe kontrole: znajdowanie wersji platformy Docker, ocena stanu demona platformy Docker i uruchamianie polecenia ściągania platformy Docker.

*Potencjalne rozwiązania:* Zainstaluj klienta platformy Docker; dodaj ścieżkę platformy Docker do zmiennych systemowych.

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

Ten błąd oznacza, że stan demona platformy Docker jest niedostępny lub że nie można go osiągnąć przy użyciu interfejsu wiersza polecenia. W rezultacie operacje platformy Docker `docker login` `docker pull`(takie jak i ) są niedostępne za pośrednictwem interfejsu wiersza polecenia.

*Potencjalne rozwiązania:* Uruchom ponownie demon platformy Docker lub sprawdź, czy jest poprawnie zainstalowany.

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

Ten błąd oznacza, że funkcja CLI `docker --version`nie może uruchomić polecenia .

*Potencjalne rozwiązania:* Spróbuj uruchomić polecenie ręcznie, upewnij się, że masz najnowszą wersję interfejsu wiersza polecenia i zbadaj komunikat o błędzie.

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

Ten błąd oznacza, że wiersz polecenia nie był w stanie pobrać przykładowy obraz do środowiska.

*Potencjalne rozwiązania:* Sprawdź, czy wszystkie składniki niezbędne do wyciągnięcia obrazu działają prawidłowo.

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

Ten błąd oznacza, że nie można odnaleźć klienta Helm przez wiersz polecenia, co wyklucza inne operacje Helm.

*Potencjalne rozwiązania:* Sprawdź, czy klient helm jest zainstalowany i czy jego ścieżka jest dodawana do zmiennych środowiska systemowego.

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

Ten błąd oznacza, że wiersz polecenia nie może określić zainstalowanej wersji Helm. Może się tak zdarzyć, jeśli wersja interfejsu wiersza polecenia platformy Azure (lub jeśli używana wersja Helm) jest przestarzała.

*Potencjalne rozwiązania:* Zaktualizuj do najnowszej wersji interfejsu wiersza polecenia platformy Azure lub do zalecanej wersji helm; uruchomić polecenie ręcznie i zbadać komunikat o błędzie.

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

Ten błąd oznacza, że system DNS dla danego serwera logowania rejestru został pingowany, ale nie odpowiedział, co oznacza, że jest niedostępny. Może to wskazywać na pewne problemy z łącznością. Alternatywnie rejestr może nie istnieć, użytkownik może nie mieć uprawnień do rejestru (aby poprawnie pobrać jego serwer logowania) lub rejestr docelowy znajduje się w innej chmurze niż ten używany w interfejsie wiersza polecenia platformy Azure.

*Potencjalne rozwiązania*: Sprawdź poprawność łączności; sprawdza pisownię rejestru i czy rejestr istnieje; sprawdź, czy użytkownik ma odpowiednie uprawnienia do niego i czy chmura rejestru jest taka sama, która jest używana w interfejsie wiersza polecenia platformy Azure.

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

Ten błąd oznacza, że punkt końcowy wyzwania dla danego rejestru odpowiedział stan 403 Zabronione HTTP. Ten błąd oznacza, że użytkownicy nie mają dostępu do rejestru, najprawdopodobniej z powodu konfiguracji sieci wirtualnej. Aby wyświetlić aktualnie skonfigurowane `az acr show --query networkRuleSet --name <registry>`reguły zapory, uruchom program .

*Potencjalne rozwiązania:* Usuń reguły sieci wirtualnej lub dodaj bieżący adres IP klienta do listy dozwolonych.

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

Ten błąd oznacza, że punkt końcowy wyzwania rejestru docelowego nie wydał wyzwania.

*Potencjalne rozwiązania*: Spróbuj ponownie po pewnym czasie. Jeśli błąd będzie się powtarzał, otwórz problem w https://aka.ms/acr/issuespliku .

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Ten błąd oznacza, że punkt końcowy wyzwania rejestru docelowego wystawił wyzwanie, ale rejestr nie obsługuje uwierzytelniania usługi Azure Active Directory.

*Potencjalne rozwiązania:* spróbuj użyć innego sposobu uwierzytelniania, na przykład za pomocą poświadczeń administratora. Jeśli użytkownicy muszą uwierzytelniać się przy użyciu https://aka.ms/acr/issuesusługi Azure Active Directory, otwórz problem w pliku .

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Ten błąd oznacza, że serwer logowania rejestru nie odpowiedział tokenem odświeżania, więc odmowa dostępu do rejestru docelowego. Ten błąd może wystąpić, jeśli użytkownik nie ma odpowiednich uprawnień do rejestru lub jeśli poświadczenia użytkownika dla interfejsu wiersza polecenia platformy Azure są przestarzałe.

*Potencjalne rozwiązania:* Sprawdź, czy użytkownik ma odpowiednie uprawnienia do rejestru; uruchom, `az login` aby odświeżyć uprawnienia, tokeny i poświadczenia.

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Ten błąd oznacza, że serwer logowania rejestru nie odpowiedział tokenem dostępu, więc odmówiono dostępu do rejestru docelowego. Ten błąd może wystąpić, jeśli użytkownik nie ma odpowiednich uprawnień do rejestru lub jeśli poświadczenia użytkownika dla interfejsu wiersza polecenia platformy Azure są przestarzałe.

*Potencjalne rozwiązania:* Sprawdź, czy użytkownik ma odpowiednie uprawnienia do rejestru; uruchom, `az login` aby odświeżyć uprawnienia, tokeny i poświadczenia.

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

Ten błąd oznacza, że klient nie może ustanowić bezpiecznego połączenia z rejestrem kontenerów. Ten błąd zazwyczaj występuje, jeśli używasz lub używasz serwera proxy.

*Potencjalne rozwiązania*: Więcej informacji na temat pracy za serwerem proxy można [znaleźć tutaj](https://github.com/Azure/azure-cli/blob/master/doc/use_cli_effectively.md#working-behind-a-proxy).

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

Ten błąd oznacza, że interfejs wiersza polecenia nie może znaleźć serwera logowania danego rejestru i nie znaleziono domyślnego sufiksu dla bieżącej chmury. Ten błąd może wystąpić, jeśli rejestr nie istnieje, jeśli użytkownik nie ma odpowiednich uprawnień do rejestru, jeśli chmura rejestru i bieżąca chmura interfejsu wiersza polecenia platformy Azure nie są zgodne lub jeśli wersja interfejsu wiersza polecenia platformy Azure jest przestarzała.

*Potencjalne rozwiązania:* Sprawdź, czy pisownia jest poprawna i czy rejestr istnieje; sprawdź, czy użytkownik ma odpowiednie uprawnienia do rejestru i czy chmury rejestru i środowiska interfejsu wiersza polecenia są zgodne; aktualizacji interfejsu wiersza polecenia platformy Azure do najnowszej wersji.

## <a name="notary_version_error"></a>NOTARY_VERSION_ERROR

Ten błąd oznacza, że wiersz polecenia nie jest zgodny z aktualnie zainstalowaną wersją platformy Docker/Notariusz. Spróbuj obniżyć wersję notary.exe do wersji wcześniejszej niż 0.6.0, ręcznie zastępując klienta notariusza instalacji platformy Docker, aby rozwiązać ten problem.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać opcje sprawdzania kondycji rejestru, zobacz [Sprawdzanie kondycji rejestru kontenerów platformy Azure.](container-registry-check-health.md)

Zobacz [często](container-registry-faq.md) zadawane pytania dotyczące często zadawanych pytań i innych znanych problemów dotyczących rejestru kontenerów platformy Azure.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
