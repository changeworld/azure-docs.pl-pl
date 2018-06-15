---
title: Szybki start — tworzenie rejestru prywatnego platformy Docker na platformie Azure przy użyciu witryny Azure Portal
description: Szybka nauka tworzenia rejestru prywatnego platformy Docker przy użyciu witryny Azure Portal.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 55da52e0d314c353c669c56ad918c4dd6bef44c3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32169809"
---
# <a name="quickstart-create-a-container-registry-using-the-azure-portal"></a>Szybki start: tworzenie rejestru kontenerów za pomocą witryny Azure Portal

Usługa Azure Container Registry to rejestr prywatny platformy Docker na platformie Azure, w którym można przechowywać prywatne obrazy kontenerów Docker i zarządzać nimi. W tym przewodniku Szybki start utworzysz rejestr kontenerów za pomocą witryny Azure Portal, wypchniesz obraz kontenera do rejestru i na koniec wdrożysz kontener z rejestru do usługi Azure Container Instances (ACI).

Aby ukończyć ten przewodnik Szybki start, musisz mieć platformę Docker zainstalowaną lokalnie. Środowisko Docker zawiera pakiety, które umożliwiają łatwe konfigurowanie platformy Docker w systemie [Mac][docker-mac], [Windows][docker-windows] lub [Linux][docker-linux].

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

Wybierz pozycję **Utwórz zasób** > **Kontenery** > **Azure Container Registry**.

![Tworzenie rejestru kontenerów w witrynie Azure Portal][qs-portal-01]

Wprowadź wartości w polach **Nazwa rejestru** i **Grupa zasobów**. Nazwa rejestru musi być unikatowa w obrębie platformy Azure i może zawierać od 5 do 50 znaków alfanumerycznych. Utwórz nową grupę zasobów o nazwie `myResourceGroup`, a w polu **SKU** wybierz opcję „Basic” (Podstawowa). Wybierz przycisk **Utwórz**, aby wdrożyć wystąpienie usługi ACR.

![Tworzenie rejestru kontenerów w witrynie Azure Portal][qs-portal-03]

W tym przewodniku Szybki start utworzymy rejestr w ramach jednostki *Podstawowa*. Usługa Azure Container Registry jest dostępna w ramach kilku różnych jednostek SKU, krótko opisanych w poniższej tabeli. Aby uzyskać szczegółowe informacji o każdej z nich, zobacz [Jednostki SKU rejestru kontenerów][container-registry-skus].

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Kiedy pojawi się komunikat **Wdrażanie zakończyło się pomyślnie**, wybierz rejestru kontenerów w portalu, a następnie wybierz pozycję **Klucze dostępu**.

![Tworzenie rejestru kontenerów w witrynie Azure Portal][qs-portal-05]

W obszarze **Administrator** wybierz pozycję **Włącz**. Zanotuj następujące wartości:

* Serwer logowania
* Nazwa użytkownika
* hasło

Te wartości będą używane w poniższych krokach podczas pracy z rejestrem z poziomu interfejsu wiersza polecenia platformy Docker.

![Tworzenie rejestru kontenerów w witrynie Azure Portal][qs-portal-06]

## <a name="log-in-to-acr"></a>Zaloguj się do usługi ACR

Przed wypychaniem i ściąganiem obrazów kontenerów musisz zalogować się do wystąpienia usługi ACR. Aby to zrobić, użyj polecenia [docker login][docker-login]. Zastąp wartości *username*, *password* i *login server* wartościami zanotowanymi w poprzednim kroku.

```bash
docker login --username <username> --password <password> <login server>
```

Po ukończeniu polecenie zwraca ciąg `Login Succeeded`. Może także zostać wyświetlone ostrzeżenie o zabezpieczeniach zalecające użycie parametru `--password-stdin`. Użycie tego parametru wykracza poza zakres tego artykułu, jednak zalecamy zastosowanie tego najlepszego rozwiązania. Zobacz polecenie [docker login][docker-login], aby uzyskać więcej informacji.

## <a name="push-image-to-acr"></a>Wypychanie obrazu do usługi ACR

Aby wypchnąć obrazu do usługi Azure Container Registry, najpierw musisz mieć obraz. W razie potrzeby uruchom poniższe polecenie, aby ściągnąć istniejący obraz z usługi Docker Hub.

```bash
docker pull microsoft/aci-helloworld
```

Przed wypchnięciem obrazu do rejestru musisz otagować obraz przy użyciu nazwy serwera logowania usługi ACR. Aby dodać tag do obrazu, użyj polecenia [docker tag][docker-tag]. Zastąp element *login server* zapisaną wcześniej nazwą serwera logowania.

```bash
docker tag microsoft/aci-helloworld <login server>/aci-helloworld:v1
```

Na koniec użyj polecenia [docker push][docker-push], aby wypchnąć obraz do wystąpienia usługi ACR. Zastąp element *login server* nazwą serwera logowania wystąpienia usługi ACR.

```bash
docker push <login server>/aci-helloworld:v1
```

Dane wyjściowe z pomyślnie ukończonego polecenia `docker push` są podobne do następujących:

```
The push refers to a repository [uniqueregistryname.azurecr.io/aci-helloworld]
7c701b1aeecd: Pushed
c4332f071aa2: Pushed
0607e25cc175: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:f2867748615cc327d31c68b1172cc03c0544432717c4d2ba2c1c2d34b18c62ba size: 1577
```

## <a name="list-container-images"></a>Tworzenie listy obrazów kontenerów

Aby wyświetlić listę obrazów w wystąpieniu usługi ACR, przejdź do rejestru w portalu i wybierz pozycję **Repozytoria**, następnie wybierz repozytorium utworzone za pomocą `docker push`.

W tym przykładzie wybierz repozytorium **aci-helloworld**. Zobaczysz obraz z tagiem `v1` w obszarze **TAGS** (tagi).

![Tworzenie rejestru kontenerów w witrynie Azure Portal][qs-portal-09]

## <a name="deploy-image-to-aci"></a>Wdrażanie obrazu w usłudze ACI

W celu wdrożenia do wystąpienia z rejestru trzeba przejść do repozytorium (aci helloworld), a następnie kliknąć przycisk wielokropka obok wersji v1.

![Uruchamianie wystąpienia kontenera platformy Azure z portalu][qs-portal-10]

Z wyświetlonego menu kontekstowego wybierz pozycję **Uruchom wystąpienie**:

![Menu kontekstowe uruchamiania usługi ACI][qs-portal-11]

Wypełnij pole **Nazwa kontenera**, upewnij się, że wybrana jest poprawna subskrypcja, wybierz istniejącą **grupę zasobów**: „myResourceGroup”, a następnie kliknij przycisk **OK** w celu uruchomienia wystąpienia kontenera platformy Azure.

![Opcje uruchamiania wdrażania w usłudze ACI][qs-portal-12]

Po rozpoczęciu wdrażania na pulpicie nawigacyjnym portalu pojawi się kafelek wskazujący postęp wdrażania. Po zakończeniu wdrażania ten kafelek zostanie zaktualizowany i będzie zawierał nową grupę kontenerów **mycontainer**.

![Stan wdrożenia w usłudze ACI][qs-portal-13]

Wybierz grupę kontenerów mycontainer, aby wyświetlić jej właściwości. Zanotuj **Adres IP** grupy kontenerów oraz **STAN** kontenera.

![Szczegóły kontenera usługi ACI][qs-portal-14]

## <a name="view-the-application"></a>Wyświetlanie aplikacji

Gdy kontener będzie w stanie **Uruchomiono**, w ulubionej przeglądarce przejdź do adresu IP zanotowanego w poprzednim kroku, aby wyświetlić aplikację.

![Aplikacja Hello World w przeglądarce][qs-portal-15]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby wyczyścić zasoby, przejdź do grupy zasobów **myResourceGroup** w portalu. Po załadowaniu grupy zasobów kliknij pozycję **Usuń grupę zasobów** w celu usunięcia grupy zasobów, usługi Azure Container Registry i wszystkich wystąpień kontenerów platformy Azure.

![Tworzenie rejestru kontenerów w witrynie Azure Portal][qs-portal-08]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzona została usługa Azure Container Registry z interfejsem wiersza polecenia platformy Azure i uruchomione zostało jej wystąpienie za pośrednictwem usługi Azure Container Instances. Przejdź do samouczka usługi Azure Container Instances, aby dowiedzieć się więcej o tej usłudze.

> [!div class="nextstepaction"]
> [Samouczki dotyczące usługi Azure Container Instances][container-instances-tutorial-prepare-app]

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-04]: ./media/container-registry-get-started-portal/qs-portal-04.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-06]: ./media/container-registry-get-started-portal/qs-portal-06.png
[qs-portal-07]: ./media/container-registry-get-started-portal/qs-portal-07.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png
[qs-portal-10]: ./media/container-registry-get-started-portal/qs-portal-10.png
[qs-portal-11]: ./media/container-registry-get-started-portal/qs-portal-11.png
[qs-portal-12]: ./media/container-registry-get-started-portal/qs-portal-12.png
[qs-portal-13]: ./media/container-registry-get-started-portal/qs-portal-13.png
[qs-portal-14]: ./media/container-registry-get-started-portal/qs-portal-14.png
[qs-portal-15]: ./media/container-registry-get-started-portal/qs-portal-15.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md
