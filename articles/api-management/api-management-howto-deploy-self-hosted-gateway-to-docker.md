---
title: Wdróż samodzielną bramę usługi Azure API Management z platformą Docker | Microsoft Docs
description: Dowiedz się, jak wdrożyć samohostowaną bramę usługi Azure API Management na platformie Docker
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: fc7e0f7e4e0e80a030a437bc4f0f13360595f32e
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747693"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Wdrażanie bramy samohostowanej na platformie Azure API Management w usłudze Docker

W tym artykule przedstawiono kroki wdrażania samodzielnej bramy usługi Azure API Management w środowisku Docker.

> [!NOTE]
> Funkcja samoobsługowego bramy jest dostępna w wersji zapoznawczej. W wersji zapoznawczej Brama samoobsługowa jest dostępna tylko w warstwach deweloper i Premium bez dodatkowych opłat. Warstwa dewelopera jest ograniczona do jednego wdrożenia bramy samoobsługowego.

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md)
- Utwórz środowisko platformy Docker. Platforma [Docker dla komputerów stacjonarnych](https://www.docker.com/products/docker-desktop) jest dobrą opcją do celów deweloperskich i testowych. Zapoznaj się z [dokumentacją platformy Docker](https://docs.docker.com) , aby uzyskać informacje na temat wszystkich wersji platformy Docker, ich funkcji oraz kompleksowej dokumentacji dotyczącej platformy Docker.
- [Inicjowanie obsługi administracyjnej zasobu bramy w wystąpieniu API Management](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> Brama samoobsługowa jest spakowana jako kontener platformy Docker oparty na systemie Linux.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Wdrażanie bramy samohostowanej na platformie Docker

1. Wybierz pozycję **bramy** z sekcji **Ustawienia**.
2. Wybierz zasób bramy, który ma zostać wdrożony.
3. Wybierz pozycję **wdrożenie**.
4. Należy zauważyć, że nowy token w polu tekstowym **token** został wygenerowany automatycznie przy użyciu domyślnych wartości kluczy **wygaśnięcia** i **tajnych** . Dostosuj jedną lub obie w razie potrzeby, a następnie wybierz pozycję **Generuj** , aby utworzyć nowy token.
4. Upewnij się, że w obszarze **Skrypty wdrażania**wybrano **platformę Docker** .
5. Wybierz łącze plik **ENV. conf** obok **środowiska** , aby pobrać plik.
6. Wybierz ikonę **Kopiuj** znajdującą się po prawej stronie pola tekstowego **Uruchom** , aby zapisać polecenie Docker w Schowku.
7. Wklej polecenie do okna terminalu (lub polecenia). Dostosuj mapowania portów i nazwę kontenera odpowiednio do wymagań. Należy pamiętać, że polecenie oczekuje, że pobrany plik środowiska będzie obecny w bieżącym katalogu.
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. Wykonaj polecenie. Polecenie instruuje środowisko Docker, aby uruchomić kontener, przy użyciu wstępnie hostowanego obrazu bramy pobranego z programu Microsoft Container Registry i mapowania portów HTTP (8080) i HTTPS (8081) do portów 80 i 443 na hoście.
9. Uruchom poniższe polecenie, aby sprawdzić, czy brama pod jest uruchomiona:
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:beta   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Wróć do Azure Portal i upewnij się, że wdrożony węzeł bramy jest w stanie dobra kondycja.

![Stan bramy](media/api-management-howto-deploy-self-hosted-gateway-to-docker/status.png)

> [!TIP]
> Użyj <code>console docker container logs <gateway-name></code> polecenia, aby wyświetlić migawkę dziennika bramy samohostowanej.
>
> Użyj <code>docker container logs --help</code> polecenia, aby wyświetlić wszystkie opcje wyświetlania dzienników.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat bramy samoobsługowej, zobacz [Omówienie usługi Azure API Management Brama](self-hosted-gateway-overview.md)samodzielna.
* [Skonfiguruj niestandardową nazwę domeny dla bramy samohostowanej](api-management-howto-configure-custom-domain-gateway.md).
