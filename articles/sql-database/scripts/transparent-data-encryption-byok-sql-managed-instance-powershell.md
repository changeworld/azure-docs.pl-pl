---
title: 'Program PowerShell: Włączanie funkcji TDE BYOK - wystąpienie zarządzane usługi Azure SQL Database | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować wystąpienia zarządzanego Azure SQL Aby rozpocząć korzystanie z funkcji BYOK przezroczystego szyfrowania danych (TDE) do szyfrowania podczas spoczynku przy użyciu programu PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: d428c74a486c4bcb6ea1568fbb8abd813960c708
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52974983"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Zarządzanie przezroczyste szyfrowanie danych w wystąpieniu zarządzanym przy użyciu własnego klucza z usługi Azure Key Vault

Ten przykładowy skrypt programu PowerShell konfiguruje przezroczystego szyfrowania danych (TDE) w scenariuszu własnego klucza dla wystąpienia zarządzanego Azure SQL, używając klucza z usługi Azure Key Vault. Aby dowiedzieć się więcej na temat funkcji TDE z obsługą Bring Your Own Key (BYOK), zobacz [TDE własnego klucza do bazy danych SQL Azure](../transparent-data-encryption-byok-azure-sql.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek będzie wymagał modułu programu Azure PowerShell w wersji 5.7.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

Jeśli zdecydujesz się zainstalować i korzystać z programu PowerShell lokalnie, ten samouczek wymaga również wersję zapoznawczą pakietami programu PowerShell elementu AzureRM.Sql *4.11.6-preview*. Uruchom następujące polecenie, aby ją zainstalować: `Install-Module -Name AzureRM.Sql -RequiredVersion 4.11.6-preview -AllowPrerelease`

## <a name="sample-scripts"></a>Przykładowe skrypty

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell dla usługi SQL Database można znaleźć w [skryptach programu PowerShell dla usługi Azure SQL Database](../sql-database-powershell-samples.md).
