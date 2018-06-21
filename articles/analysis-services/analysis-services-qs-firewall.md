---
title: Szybki Start — Konfigurowanie zapory na potrzeby serwera usług Analysis Services na platformie Azure | Microsoft Docs
description: Dowiedz się, jak skonfigurować zaporę na potrzeby wystąpienia serwera usług Analysis Services na platformie Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 05/23/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a7a7a933a37994157e9d566802de007b3e4bccb1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636046"
---
# <a name="quickstart-configure-server-firewall---portal"></a>Szybki start: konfigurowanie zapory serwera — Portal

Ten przewodnik Szybki start ułatwia konfigurowanie zapory na potrzeby serwera usług Azure Analysis Services. Włączanie zapory i konfiguracja zakresów adresów IP tylko na potrzeby komputerów uzyskujących dostęp do serwera użytkownika są ważnym elementem zabezpieczenia serwera i danych.

## <a name="prerequisites"></a>Wymagania wstępne

- Serwer usług Analysis Services w ramach subskrypcji. Więcej informacji znajduje się w tematach [Quickstart: Create a server - Portal](analysis-services-create-server.md) (Szybki start: tworzenie serwera — Portal) oraz [Quickstart: Create a server - PowerShell](analysis-services-create-powershell.md) (Szybki start: tworzenie serwera — program PowerShell)
- Co najmniej jeden zakres adresów IP na potrzeby komputerów klienckich (w razie potrzeby).

## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal 

[Logowanie do portalu](https://portal.azure.com)

## <a name="configure-a-firewall"></a>Konfigurowanie zapory

1. Kliknij serwer, aby otworzyć stronę Omówienie. 
2. Wybierz opcje **USTAWIENIA** > **Zapora** > **Włącz zaporę** i kliknij przycisk **Włączone**.
3. Aby zezwolić na dostęp w trybie DirectQuery z poziomu usługi Power BI, w obszarze **Zezwalaj na dostęp z usługi Power BI** kliknij przycisk **Włączone**.  
4. (Opcjonalnie) Podaj co najmniej jeden zakres adresów IP. Wprowadź nazwę oraz początkowy i końcowy adres IP dla każdego zakresu. 
5. Kliknij pozycję **Zapisz**.

     ![Ustawienia zapory](./media/analysis-services-qs-firewall/aas-qs-firewall.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zakresy adresów IP nie będą już potrzebne, usuń je lub wyłącz zaporę.

## <a name="next-steps"></a>Następne kroki
W tym przewodniu Szybki start opisano, jak skonfigurować zaporę dla serwera. Teraz gdy serwer został już utworzony i zabezpieczony przy użyciu zapory, możesz do niego dodać podstawowy przykładowy model danych z portalu. Przykładowy model służy do zdobywania wiedzy na temat konfigurowania ról modelowej bazy danych oraz testowania połączeń klienckich. Aby dowiedzieć się więcej, przejdź do samouczka dotyczącego dodawania przykładowego modelu.

> [!div class="nextstepaction"]
> [Tutorial: Add a sample model to your server](analysis-services-create-sample-model.md) (Samouczek: dodawanie przykładowego modelu do serwera)
