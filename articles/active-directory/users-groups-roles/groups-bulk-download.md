---
title: Pobieranie listy grup w portalu usługi Azure Active Directory | Dokumenty firmy Microsoft
description: Pobieranie właściwości grupy zbiorczo w centrum administracyjnym platformy Azure w usłudze Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 09/11/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b08e807e179270b63ca81d3777c230c3e129c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517138"
---
# <a name="bulk-download-a-list-of-groups-preview-in-azure-active-directory"></a>Pobieranie zbiorcze listy grup (wersja zapoznawcza) w usłudze Azure Active Directory

Korzystając z portalu usługi Azure Active Directory (Azure AD), można zbiorczo pobrać listę wszystkich grup w organizacji do pliku wartości oddzielonych przecinkami (CSV).

## <a name="to-download-a-list-of-groups"></a>Aby pobrać listę grup

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy za pomocą konta administratora w organizacji.
1. W usłudze Azure AD wybierz pozycję **Grupy** > **pobierania grup**.
1. Na stronie **Pobieranie grup** wybierz pozycję **Start,** aby otrzymać plik CSV z listą grup.

   ![Polecenie Grupy pobierania znajduje się na stronie Wszystkie grupy](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Sprawdź stan pobierania

Stan wszystkich oczekujących żądań zbiorczych można zobaczyć na stronie **Wyniki operacji zbiorczej (wersja zapoznawcza).**

   ![Strona Wyniki operacji zbiorczych pokazuje stan żądania zbiorczego](./media/groups-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Limity usług pobierania zbiorczego

Każde działanie zbiorcze w celu pobrania listy grup może trwać do jednej godziny. Dzięki temu można pobrać listę co najmniej 300 000 grup.

## <a name="next-steps"></a>Następne kroki

- [Zbiorcze usuwanie członków grupy](groups-bulk-remove-members.md)
- [Pobieranie członków grupy](groups-bulk-download-members.md)
