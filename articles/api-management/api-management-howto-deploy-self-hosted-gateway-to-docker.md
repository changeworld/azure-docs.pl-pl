---
title: Wdrażanie bramy usługi Azure API Management hosted w usłudze Docker | Dokumenty firmy Microsoft
description: Dowiedz się, jak wdrożyć samodzielnie hostowane bramy usługi Azure API Management do platformy Docker
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
ms.openlocfilehash: b5acda30f7f4a01e1b7b6ac82652452c3c40e3bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768507"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Wdrażanie bramy hosta azure api management z witryną Platformy Docker

Ten artykuł zawiera kroki wdrażania samodzielnie hostowane bramy usługi Azure API Management w środowisku platformy Docker.

> [!NOTE]
> Funkcja bramy hostowanego samodzielnie jest w wersji zapoznawczej. W wersji zapoznawczej brama hostowana samodzielnie jest dostępna tylko w warstwach Deweloper i Premium bez dodatkowych opłat. Warstwa deweloperów jest ograniczona do pojedynczego wdrożenia bramy hostowanej samodzielnie.

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md)
- Tworzenie środowiska platformy Docker. [Docker for Desktop](https://www.docker.com/products/docker-desktop) jest dobrym rozwiązaniem dla celów rozwoju i oceny. Zobacz [dokumentację platformy Docker,](https://docs.docker.com) aby uzyskać informacje na temat wszystkich wersji platformy Docker, ich funkcji i kompleksowej dokumentacji dotyczącej samego platformy Docker.
- [Aprowizuj zasób bramy w wystąpieniu usługi API Management](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> Brama hostowana samodzielnie jest pakowana jako kontener platformy Docker oparty na systemie X86-64 Linux.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Wdrażanie bramy hostowanego samodzielnie w ucho.

1. Wybierz **bramy** z sekcji **Ustawienia**.
2. Wybierz zasób bramy, który zamierzasz wdrożyć.
3. Wybierz **opcję Wdrażanie**.
4. Należy zauważyć, że nowy token w polu tekstowym **token** został automatyczniegenerowany przy użyciu **domyślnych** wartości wygaśnięcia i **klucz tajny.** Dostosuj jedną lub obie w razie potrzeby i wybierz **pozycję Generuj,** aby utworzyć nowy token.
4. Upewnij się, że w obszarze **Skrypty wdrażania**wybrano pozycję **Docker** .
5. Wybierz łącze do pliku **env.conf** obok **środowiska,** aby pobrać plik.
6. Wybierz ikonę **kopiowania** znajdującą się po prawej stronie pola tekstowego **Uruchom,** aby zapisać polecenie Docker w schowku.
7. Wklej polecenie do okna terminala (lub polecenia). W razie potrzeby dostosuj mapowania portów i nazwę kontenera. Należy zauważyć, że polecenie oczekuje, że pobrany plik środowiska będzie obecny w bieżącym katalogu.
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. Wykonaj polecenie. Polecenie nakazuje środowisku platformy Docker uruchomienie kontenera przy użyciu obrazu bramy hostowanego samodzielnie pobranego z rejestru kontenerów firmy Microsoft oraz mapowanie portów HTTP (8080) i HTTPS (8081) kontenera na porty 80 i 443 na hoście.
9. Uruchom poniższe polecenie, aby sprawdzić, czy zasobnik bramy jest uruchomiony:
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:beta   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Wróć do witryny Azure portal i upewnij się, że właśnie wdrożony węzeł bramy zgłasza zdrowy stan.

![stan bramy](media/api-management-howto-deploy-self-hosted-gateway-to-docker/status.png)

> [!TIP]
> Polecenie <code>console docker container logs <gateway-name></code> służy do wyświetlania migawki dziennika bramy hostowanego samodzielnie.
>
> Użyj <code>docker container logs --help</code> polecenia, aby wyświetlić wszystkie opcje wyświetlania dziennika.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o bramie hostowanej samodzielnie, zobacz [Omówienie bramy hostowanego przez](self-hosted-gateway-overview.md)usługę Azure API Management.
* [Skonfiguruj niestandardową nazwę domeny dla bramy hostowanego samodzielnie](api-management-howto-configure-custom-domain-gateway.md).
