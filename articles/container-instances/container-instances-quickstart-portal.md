---
title: Szybki start — wdrażanie kontenera platformy Docker w wystąpieniu kontenera — portal
description: W tym przewodniku Szybki start można użyć portalu Azure do szybkiego wdrożenia konteneryzowanej aplikacji sieci web, która działa w izolowanym wystąpieniu kontenera platformy Azure
ms.topic: quickstart
ms.date: 03/09/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: 7a872e955db46b76d3b12f8ffc38d4a8e497ea63
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79087955"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Szybki start: wdrażanie wystąpienia kontenera na platformie Azure przy użyciu witryny Azure portal

Użyj wystąpienia kontenera platformy Azure do uruchamiania kontenerów platformy Docker bez użycia serwera z prostotą i szybkością. Wdrażanie aplikacji do wystąpienia kontenera na żądanie, gdy nie potrzebujesz pełnej platformy aranżacji kontenera, takiej jak usługa Azure Kubernetes.

W tym przewodniku Szybki start można użyć witryny Azure portal do wdrożenia kontenera platformy Docker i udostępnić jego aplikację z w pełni kwalifikowaną nazwą domeny (FQDN). Po skonfigurowaniu kilku ustawień i wdrożeniu kontenera możesz przejść do uruchomionej aplikacji:

![Aplikacja wdrożona w usłudze Azure Container Instances widziana w przeglądarce][aci-portal-07]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto][azure-free-account] przed rozpoczęciem.

## <a name="create-a-container-instance"></a>Tworzenie wystąpienia kontenera

Wybierz **opcję Utwórz** > **wystąpienie kontenerów kontenerów** > **kontenerów**.

![Rozpoczynanie tworzenia nowego wystąpienia kontenera w witrynie Azure Portal][aci-portal-01]

Na stronie Podstawy wprowadź następujące wartości w **polach** tekstowych **Grupa Zasobów**, **Nazwa kontenera**i Obraz **kontenera.** Pozostaw domyślne wartości w pozostałych polach i kliknij przycisk **OK**.

* Grupa zasobów: **Tworzenie nowych** > `myresourcegroup`
* Nazwa kontenera: `mycontainer`
* Źródło obrazu: **obrazy szybki start**
* Obraz kontenera: `mcr.microsoft.com/azuredocs/aci-helloworld` (Linux)

![Konfigurowanie podstawowych ustawień nowego wystąpienia kontenera w witrynie Azure Portal][aci-portal-03]

W przypadku tego przewodnika Szybki start można `aci-helloworld` użyć ustawień domyślnych do wdrożenia publicznego obrazu firmy Microsoft. Ten przykładowy obraz systemu Linux pakiety małej aplikacji sieci web napisanej w Node.js, który służy statycznej strony HTML. Można również przynieść własne obrazy kontenerów przechowywane w usłudze Azure Container Registry, Docker Hub lub innych rejestrach.

Na stronie **Sieć** określ **etykietę nazwy DNS** kontenera. Nazwa musi być unikatowa w regionie platformy Azure, w którym utworzysz wystąpienie kontenera. Twój kontener będzie dostępny publicznie pod adresem `<dns-name-label>.<region>.azurecontainer.io`. Jeśli zostanie wyświetlony komunikat o błędzie „Etykieta nazwy DNS nie jest dostępna”, spróbuj użyć innej etykiety nazwy DNS.

![Konfigurowanie ustawień sieciowych dla nowego wystąpienia kontenera w witrynie Azure portal][aci-portal-04]

Pozostaw inne ustawienia przy ich wartościach domyślnych, a następnie wybierz **pozycję Przejrzyj + utwórz**.

Po zakończeniu weryfikacji zostanie wyświetlone podsumowanie ustawień kontenera. Wybierz **pozycję Utwórz,** aby przesłać żądanie wdrożenia kontenera.

![Podsumowanie ustawień nowego wystąpienia kontenera w witrynie Azure Portal][aci-portal-05]

Po uruchomieniu wdrożenia pojawia się powiadomienie wskazujące, że wdrożenie jest w toku. Kolejne powiadomienie zostanie wyświetlone, gdy grupa kontenerów zostanie wdrożona.

Otwórz omówienie grupy kontenerów, przechodząc do **grupy** > zasobów**myresourcegroup** > **mycontainer**. Zwróć uwagę na wartość **FQDN** (w pełni kwalifikowana nazwa domeny) wystąpienia kontenera, a także na jego **Stan**.

![Przegląd grupy kontenerów w witrynie Azure Portal][aci-portal-06]

Po osiągnięciu przez **Stan** wartości *Uruchomiono* przejdź w przeglądarce do nazwy FQDN kontenera.

![Widziana w przeglądarce aplikacja wdrożona za pomocą usługi Azure Container Instances][aci-portal-07]

Gratulacje! Konfigurując zaledwie kilku ustawień, udało Ci się wdrożyć publicznie dostępną aplikację w usłudze Azure Container Instances.

## <a name="view-container-logs"></a>Wyświetlanie dzienników kontenerów

W przypadku rozwiązywania problemów z kontenerem lub uruchomioną w nim aplikacją przydatne jest przeglądanie dzienników wystąpienia kontenera.

Aby wyświetlić dzienniki kontenera, w obszarze **Ustawienia**wybierz pozycję **Kontenery**, a następnie **dzienniki**. Powinno pojawić się żądanie HTTP GET generowane podczas wyświetlania aplikacji w przeglądarce.

![Dzienniki kontenera w witrynie Azure Portal][aci-portal-11]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z kontenerem wybierz opcję **Przegląd** dla wystąpienia kontenera *mycontainer*, a następnie wybierz polecenie **Usuń**.

![Usuwanie wystąpienia kontenera w witrynie Azure Portal][aci-portal-09]

Po pojawieniu się okna dialogowego potwierdzenia wybierz pozycję **Tak**.

![Potwierdzanie usunięcia wystąpienia kontenera w witrynie Azure Portal][aci-portal-10]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono wystąpienie kontenera platformy Azure na podstawie publicznego obrazu firmy Microsoft. Jeśli chcesz skompilować obraz kontenera i wdrożyć go za pomocą prywatnej usługi Azure Container Registry, przejdź do samouczka dotyczącego usługi Azure Container Instances.

> [!div class="nextstepaction"]
> [Samouczek dotyczący usługi Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-portal-07]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png
[aci-portal-09]: ./media/container-instances-quickstart-portal/qs-portal-09.png
[aci-portal-10]: ./media/container-instances-quickstart-portal/qs-portal-10.png
[aci-portal-11]: ./media/container-instances-quickstart-portal/qs-portal-11.png

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/