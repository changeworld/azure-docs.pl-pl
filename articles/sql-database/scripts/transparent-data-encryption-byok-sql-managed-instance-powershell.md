---
title: 'Program PowerShell: Włącz BYOK TDE-Azure SQL Database wystąpienia zarządzanego | Microsoft Docs'
description: Dowiedz się, jak skonfigurować wystąpienie zarządzane usługi Azure SQL, aby rozpocząć korzystanie z BYOK Transparent Data Encryption (TDE) na potrzeby szyfrowania w środowisku REST przy użyciu programu PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 04/19/2019
ms.openlocfilehash: d8f0f4a8e603a9040d166b00682077cff23abd8d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569697"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault-preview"></a>Zarządzanie Transparent Data Encryption w wystąpieniu zarządzanym przy użyciu własnego klucza z poziomu Azure Key Vault (wersja zapoznawcza)

Ten przykład skryptu programu PowerShell służy do konfigurowania Transparent Data Encryption (TDE) w scenariuszu Bring Your Own Key (wersja zapoznawcza) dla wystąpienia zarządzanego usługi Azure SQL przy użyciu klucza z Azure Key Vault. Aby dowiedzieć się więcej na temat obsługi TDE z Bring Your Own Key (BYOK), zobacz [TDE Bring Your Own Key do usługi Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Istniejące wystąpienie zarządzane. Zobacz [Używanie programu PowerShell do tworzenia Azure SQL Database wystąpienia zarządzanego](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Korzystanie z programu PowerShell lokalnie lub przy użyciu Azure Cloud Shell wymaga AZ PowerShell 1.1.1-Preview lub nowszej wersji zapoznawczej. Jeśli musisz przeprowadzić uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps)lub uruchamianie poniższego przykładowego skryptu w celu zainstalowania modułu.

`Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease -Force`

Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="sample-scripts"></a>Przykładowe skrypty

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell dla usługi SQL Database można znaleźć w [skryptach programu PowerShell dla usługi Azure SQL Database](../sql-database-powershell-samples.md).
