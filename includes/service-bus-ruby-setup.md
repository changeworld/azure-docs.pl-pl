---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: 16ce537a54fc77fc0f72b859d6d193501d86c1fc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "67183464"
---
## <a name="create-a-ruby-application"></a>Tworzenie aplikacji Ruby
Aby uzyskać instrukcje, zobacz [Tworzenie aplikacji Ruby na platformie Azure](../articles/virtual-machines/linux/classic/ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurowanie aplikacji do korzystania z usługi Service Bus
Aby korzystać z usługi Service Bus, pobierz i użyj pakietu Ruby platformy Azure, który zawiera zestaw bibliotek wygody, które komunikują się z usługami REST magazynu.

### <a name="use-rubygems-to-obtain-the-package"></a>Używanie narzędzia RubyGems do pobierania pakietu
1. Użyj interfejsu wiersza polecenia, takiego jak **PowerShell** (system Windows), **Terminal** (system Mac) lub **Bash** (system Unix).
2. Wpisz "gem install azure" w oknie poleceń, aby zainstalować klejnot i zależności.

### <a name="import-the-package"></a>Importowanie pakietu
Korzystając z ulubionego edytora tekstu, dodaj następujące elementy do górnej części pliku Ruby, w którym zamierzasz korzystać z pamięci masowej:

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Konfigurowanie połączenia usługi Service Bus
Użyj następującego kodu, aby ustawić wartości obszaru nazw, nazwy klucza, klucza, sygnatariusza i hosta:

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

Ustaw wartość obszaru nazw na wartość utworzoną, a nie cały adres URL. Na przykład użyj **"yourexamplenamespace",** a nie "yourexamplenamespace.servicebus.windows.net".
