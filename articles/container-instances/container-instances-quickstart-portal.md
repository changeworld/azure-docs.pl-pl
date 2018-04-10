---
title: Szybki start — Tworzenie pierwszego kontenera usługi Azure Container Instances w witrynie Azure Portal
description: W tym przewodniku Szybki start witryna Azure Portal zostanie użyta do wdrożenia kontenera w usłudze Azure Container Instances
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: quickstart
ms.date: 04/02/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: cb0c8c5f5730ae1f7a2e9b38c3ef3e04ee8cde67
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>Szybki start: Tworzenie pierwszego kontenera w usłudze Azure Container Instances

Usługa Azure Container Instances ułatwia tworzenie kontenerów i zarządzanie nimi na platformie Azure. Podczas pracy z tym przewodnikiem Szybki start utworzysz kontener na platformie Azure i ujawnisz go w Internecie przy użyciu publicznego adresu IP. Te czynności wykonywane są w witrynie Azure Portal. Wystarczy kilka kliknięć, a w przeglądarce zobaczysz następujący wynik:

![Widziana w przeglądarce aplikacja wdrożona za pomocą usługi Azure Container Instances][aci-portal-07]

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal na stronie http://portal.azure.com.

## <a name="create-a-container-instance"></a>Tworzenie wystąpienia kontenera

Wybierz pozycję **Utwórz zasób** > **Kontenery** > **Azure Container Instances**.

![Rozpoczynanie tworzenia nowego wystąpienia kontenera w witrynie Azure Portal][aci-portal-01]

Wprowadź następujące wartości w polach tekstowych **Nazwa kontenera**, **Obraz kontenera** oraz **Grupa zasobów**. Pozostaw domyślne wartości w pozostałych polach i kliknij przycisk **OK**.

* Nazwa kontenera: `mycontainer`
* Obraz kontenera: `microsoft/aci-helloworld`
* Grupa zasobów: `myResourceGroup`

![Konfigurowanie podstawowych ustawień nowego wystąpienia kontenera w witrynie Azure Portal][aci-portal-03]

Usługa Azure Container Instances umożliwia tworzenie kontenerów systemów Windows i Linux. W tym przewodniku Szybki start pozostawimy ustawienie domyślne **Linux**, ponieważ w poprzednim kroku wskazaliśmy kontener systemu Linux (`microsoft/aci-helloworld`).

Pozostaw domyślne wartości pozostałych ustawień w obszarze **Konfiguracja**, a następnie kliknij przycisk **OK**, aby zweryfikować konfigurację.

![Konfigurowanie nowego wystąpienia kontenera w witrynie Azure Portal][aci-portal-04]

Po zakończeniu weryfikacji zostanie wyświetlone podsumowanie ustawień kontenera. Wybierz przycisk **OK**, aby przesłać żądanie wdrożenia kontenera.

![Podsumowanie ustawień nowego wystąpienia kontenera w witrynie Azure Portal][aci-portal-05]

Po rozpoczęciu wdrażania na pulpicie nawigacyjnym portalu pojawi się kafelek wskazujący postęp wdrażania. Po zakończeniu wdrażania ten kafelek zostanie zaktualizowany i będzie zawierał nową grupę kontenerów **mycontainer-myc1**.

![Postęp tworzenia nowego wystąpienia kontenera w witrynie Azure Portal][aci-portal-08]

Wybierz grupę kontenerów **mycontainer-myc1**, aby wyświetlić jej właściwości. Zwróć uwagę na **Adres IP** grupy kontenerów oraz **STAN** kontenera.

![Przegląd grupy kontenerów w witrynie Azure Portal][aci-portal-06]

Gdy stan kontenera zmieni się na **Uruchomiono**, przejdź do adresu IP ustalonego w poprzednim kroku, aby wyświetlić aplikację hostowaną w nowym kontenerze.

![Widziana w przeglądarce aplikacja wdrożona za pomocą usługi Azure Container Instances][aci-portal-07]

## <a name="delete-the-container"></a>Usuwanie kontenera
Po zakończeniu pracy z kontenerem zaznacz grupę kontenerów **mycontainer-myc1** i kliknij pozycję **Usuń**.

![Usuwanie wystąpienia kontenera w witrynie Azure Portal][aci-portal-09]

Spowoduje to wyświetlenie okna dialogowego potwierdzenia. Wybierz pozycję **Tak** po wyświetleniu monitu.

![Potwierdzanie usunięcia wystąpienia kontenera w witrynie Azure Portal][aci-portal-10]

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
<!--[aci-portal-02]: ./media/container-instances-quickstart-portal/qs-portal-02.png-->
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-portal-07]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png
[aci-portal-09]: ./media/container-instances-quickstart-portal/qs-portal-09.png
[aci-portal-10]: ./media/container-instances-quickstart-portal/qs-portal-10.png

## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym przewodnikiem Szybki start utworzono wystąpienie kontenera usługi Azure Container Instances na podstawie obrazu z publicznego repozytorium Docker Hub. Jeśli chcesz spróbować samodzielnie skompilować kontener i wdrożyć go w usłudze Azure Container Instances za pomocą usługi Azure Container Registry, przejdź do samouczka dotyczącego usługi Azure Container Instances.

> [!div class="nextstepaction"]
> [Samouczki dotyczące usługi Azure Container Instances](./container-instances-tutorial-prepare-app.md)