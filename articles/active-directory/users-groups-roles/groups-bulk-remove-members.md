---
title: Zbiorcze usuwanie członków grupy przez przekazanie pliku CSV — Azure Active Directory | Microsoft Docs
description: Dodaj użytkowników zbiorczo w centrum administracyjnym platformy Azure.
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
ms.openlocfilehash: b153db3570f10ad5ad130dedd0bd20fe22776ed6
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910948"
---
# <a name="bulk-remove-group-members-preview-in-azure-active-directory"></a>Zbiorcze usuwanie członków grupy (wersja zapoznawcza) w Azure Active Directory

Korzystając z portalu Azure Active Directory (Azure AD), można usunąć dużą liczbę członków z grupy, używając pliku wartości rozdzielanych przecinkami (CSV) do zbiorczego usuwania elementów członkowskich grupy.

> [!NOTE]
> Operacje zbiorcze usługi Azure AD są publiczną funkcją w wersji zapoznawczej usługi Azure AD i są dostępne z dowolnym płatnym planem licencjonowania usługi Azure AD. Aby uzyskać więcej informacji na temat terminów korzystania z wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania programu Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)wersjach zapoznawczych.

## <a name="bulk-removal-service-limits"></a>Limity usługi usuwania zbiorczego

Każde działanie zbiorcze służące do usuwania listy członków grupy można uruchomić przez maksymalnie jedną godzinę. Umożliwia to usunięcie listy co najmniej 40 000 członków.

## <a name="to-bulk-remove-group-members"></a>Aby zbiorczo usuwać członków grupy

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta administratora użytkowników w organizacji. Właściciele grupy mogą również zbiorczo usuwać członków należących do nich grup.
1. W usłudze Azure AD wybierz kolejno pozycje **grupy** > **wszystkie grupy**.
1. Otwórz grupę, z której zostaną usunięte elementy członkowskie, a następnie wybierz pozycję **Członkowie**.
1. Na stronie **Członkowie** wybierz pozycję **Usuń członków** , aby pobrać, zaktualizować i przekazać plik CSV zawierający listę elementów członkowskich, które chcesz usunąć z grupy.

   ![Polecenie Usuń członków znajduje się na stronie profilu grupy](./media/groups-bulk-remove-members/remove-panel.png)

## <a name="check-removal-status"></a>Sprawdź stan usuwania

Stan wszystkich oczekujących żądań zbiorczych można zobaczyć na stronie **wyniki operacji zbiorczej (wersja zapoznawcza)** .

   ![Na stronie wyniki operacji zbiorczych jest wyświetlany stan zbiorczego żądania](./media/groups-bulk-remove-members/bulk-center.png)

## <a name="next-steps"></a>Następne kroki

- [Członkowie grupy importu zbiorczego](groups-bulk-import-members.md)
- [Pobierz członków grupy](groups-bulk-download-members.md)
