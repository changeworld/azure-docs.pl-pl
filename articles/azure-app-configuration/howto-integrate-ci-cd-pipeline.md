---
title: Integracja z potoku ciągłej integracji i dostarczania za pomocą konfiguracji aplikacji platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można wygenerować pliku konfiguracji, korzystanie z danych w konfiguracji aplikacji platformy Azure podczas ciągłej integracji i ciągłego dostarczania
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 0e6b24175ffa28b2a0f361bc46fd6c41e09cae72
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885438"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integracja z potokiem ciągłej integracji/ciągłego wdrażania

Aby zwiększyć odporność aplikacji możliwość zdalnego nie będą mogli nawiązać połączenie konfiguracji aplikacji platformy Azure, należy spakować bieżące dane konfiguracji w pliku, który jest wdrożony z aplikacją i załadować lokalnie podczas jego uruchamiania . Ta metoda gwarantuje, czy aplikacja będzie co najmniej mają przypisane wartości domyślne ustawienie. Te wartości zostaną zastąpione przez wszystkie nowsze zmiany wprowadzone w konfiguracji sklepu z aplikacjami, gdy będzie ona dostępna.

## <a name="automate-configuration-data-retrieval"></a>Automatyzowanie konfiguracji pobieranie danych

Za pomocą [ **wyeksportować** ](./howto-import-export-data.md#export-data) funkcję konfiguracji aplikacji platformy Azure, możesz zautomatyzować proces pobierania bieżące dane konfiguracji jako pojedynczy plik. Można następnie dołączyć ten plik w kroku kompilacji lub wdrożenia ciągłej integracji i ciągłego wdrażania potoku.

## <a name="next-steps"></a>Kolejne kroki

* [Szybki start: Tworzenie aplikacji sieci web ASP.NET](quickstart-aspnet-core-app.md)  
