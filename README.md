# WavesJ (Java Library)

RU | [EN](GOOGLE.RU)

## Описание библиотеки ##
Библиотека, написанная на Java, позволяющая взаимодействовать с блокчейном Waves.<br>
Она предоставляет собой набор готовых инструментов, позволяющих работать c:
- Аккаунтами;
- Транзакциями;
- Нодой Waves.

## Содержание ##
  - [Основные возможности](#основные-возможности)
  - [Начало работы](#начало-работы)
    - [Требования](#требования)
    - [Установка](#установка)
  - [Использование](#использование)
    - [Работа с аккаунтом](#работа-с-аккаунтом)
    - [Работа с транзакциями](#работа-с-транзакциями)
    - [Работа с нодой](#работа-с-нодой)

## Основные возможности ##
1. Работа с аккаунтами:
   - Создание аккаунта с публичным/приватным ключём;
   - Создание адреса кошелька.
2. Работа с транзакциями:
   - Подпись и отправка транзакций на ноду;
   - Установка скриптов на аккаунт/ассет;
   - Подпись биржевых ордеров и отправка их на матчер;
   - Создание и сжигание токенов;
   - Лизинг и спонсирование.
3. Взаимодействие с нодой Waves.
   - To be done
   
## Начало работы ##
### Требования ###
```
Версия JDK 1.8+
```
###  Установка ###
Для добавления библиотеки WavesJ в ваш проект возможны следующие вариации.

Maven:
```
<dependency>
    <groupId>com.wavesplatform</groupId>
    <artifactId>wavesj</artifactId>
    <version>1.4.1</version>
</dependency>
```
[Ссылка на библиотеку на портале Maven Central.](https://search.maven.org/artifact/com.wavesplatform/wavesj)

Gradle:
```
compile group: 'com.wavesplatform', name: 'wavesj', version: '1.4.1'
```

SBT:
```
libraryDependencies += "com.wavesplatform" % "wavesj" % "1.4.1"
```
## Использование ##
### Работа с аккаунтом ###
В качестве примера работы с аккаунтом рассмотрим:
- Генерацию seed-фразы;
- Создание приватного и публичных ключей из seed-фразы;
- Генерацию адреса кошелька из публичного ключа. 

Импортируем все необходимые зависимости:
```Java
package node;
import com.wavesplatform.crypto.Crypto;
import com.wavesplatform.transactions.account.PrivateKey;
import com.wavesplatform.transactions.account.PublicKey;
import com.wavesplatform.transactions.account.Address;
import com.wavesplatform.transactions.common.ChainId;
```
В следующей последовательности мы:
1. Генерируем случайный seed;
2. Создаем приватный ключ из seed для пользователя alice;
3. Создаем публичный ключ из приватного ключа;
4. Формируем адрес кошелька из публичного ключа в сети STAGENET.

```Java
String seed = Crypto.getRandomSeedPhrase();
PrivateKey alice = PrivateKey.fromSeed(seed);
PublicKey publicKey = PublicKey.from(alice);
Address address = Address.from(ChainId.STAGENET, alice.publicKey());
```
### Работа с транзакциями ###
**Список транзакций**<br>
На платформе Waves возможно проведение следующих типов транзакций:
   1. Токенизация:
       - [Транзакция выпуска](#issue-transaction)
       - [Транзакция довыпуска](#reissue-transaction)
       - [Транзакция установки скрипта ассета](#set-asset-script-transaction)
       - [Транзакция обновления информации ассета](#update-asset-info-transaction)
       - [Транзакция сжигания токена](#burn-transaction)
   2. Использование
       - [Транзакция перевода](#transfer-transaction)
       - [Транзакция обмена](#exchange-transaction)
       - [Транзакция массового перевода](#mass-transfer-transaction)
       - [Транзакция установки скрипта](#set-script-transaction)
       - [Транзакция вызова скрипта](#invoke-script-transaction)
       - [Транзакция данных](#data-transaction)
       - [Транзакция создания псевдонима](#create-alias-transaction)
       - [Транзакция в формате Ethereum]()
   3. Сеть
       - [Транзакция лизинга](#lease-transaction)
       - [Транзакция закрытия лизинга](#lease-cancel-transaction)
       - [Транзакция спонсирования](#sponsor-fee-transaction)

<br>
 
**Начало работы с транзакциями**<br>
Для начала работы с транзакиями, необходимо создать клиент для сети блокчейна.<br>
Выбираем одну из сетей: TESTNET, STAGENET или MAINNET.
```Java
import com.wavesplatform.wavesj.Node;
import com.wavesplatform.wavesj.Profile;
Node node = new Node(Profile.TESTNET);
```
Подробнее о [Сетях блокчейна: Mainnet, Testnet, Stagenet](https://docs.waves.tech/ru/blockchain/blockchain-network/)<br>
Генерация бесплатных токенов в тестовой сети через [Faucet](https://testnet.wavesexplorer.com/faucet).

<br>Учитывая особенности языка Java, возможны два способа реализации функций: через **builder** или **constructor**.<br>
Для каждого из методов транзакций ниже будут продемонстрированы варианты написания двумя способами.<br><br>

---

**Токенизация:**
<!-- IssueTransaction = Транзакция выпуска -->
#### `Issue Transaction` ####
Транзакция выпуска создает новый [токен](https://docs.waves.tech/ru/blockchain/token/).<br>
Подробнее про [транзакцию выпуска](https://docs.waves.tech/ru/blockchain/transaction-type/issue-transaction).

Создадим ассет с наименованием AssetName в количестве 1000 штук и двумя знаками после запятой.<br>
Далее подпишем транзакцию приватным ключем пользователя [alice](#работа-с-аккаунтом).

```Java
// Необходимые импортирования
import com.wavesplatform.transactions.common.Base64String;
import com.wavesplatform.transactions.IssueTransaction;
import com.wavesplatform.wavesj.info.IssueTransactionInfo;

// Реализация через Builder
IssueTransaction tx = IssueTransaction.builder("AssetName", 1000, 2)
.description("description").script(null).isReiussable(false)
.getSignedWith(alice);

// Реализация через Constructor
IssueTransaction tx = new IssueTransaction(alice.publicKey(),"AssetName","description",1000,2,false,null)
.addProof(alice); 

// Отправка транзакции на ноду и вывод на экране информации о ней
node.waitForTransaction(node.broadcast(tx).id());
IssueTransactionInfo txInfo = node.getTransactionInfo(tx.id(), IssueTransactionInfo.class);

System.out.println("type:" + txInfo.tx().type());
System.out.println("id:" + txInfo.tx().id());
System.out.println("fee:" + txInfo.tx().fee().value());
System.out.println("feeAssetId:" + txInfo.tx().fee().assetId().encoded());
System.out.println("timestamp:" + txInfo.tx().timestamp());
System.out.println("version:" + txInfo.tx().version());
System.out.println("chainId:" + txInfo.tx().chainId());
System.out.println("sender:" + txInfo.tx().sender().address().encoded());
System.out.println("senderPublicKey:" + txInfo.tx().sender().encoded());
System.out.println("proofs:" + txInfo.tx().proofs());
System.out.println("assetId:" + txInfo.tx().assetId().encoded());
System.out.println("name:" + txInfo.tx().name());
System.out.println("quantity:" + txInfo.tx().quantity());
System.out.println("reissuable:" + txInfo.tx().reissuable());
System.out.println("decimals:" + txInfo.tx().decimals());
System.out.println("description:" + txInfo.tx().description());
System.out.println("script:" + txInfo.tx().script().encoded());
System.out.println("height:" + txInfo.height());
System.out.println("applicationStatus:" + txInfo.applicationStatus());
```
**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
| name <span style="color:red">(required)</span> | Название токена. <br>От 4 до 16 байт (1 символ может занимать до 4 байт).| AssetName |
| quantity <span style="color:red">(required)</span> | Количество токена.<br>Целое число, выраженное в минимальных неделимых единицах токена.<br> То есть фактическое количество, умноженное на 10decimals.<br>От 1 до 9 223 372 036 854 775 807.1 для NFT.| 1000 |
| decimals <span style="color:red">(required)</span> | Количество знаков после запятой, от 0 до 8. <br>0 для NFT.| 2 |
| description | Описание токена. От 0 до 1000 байт.| Description|
| reissuable | Флаг возможности довыпуска, см.<br>[Транзакция довыпуска](https://docs.waves.tech/ru/blockchain/transaction-type/reissue-transaction). false для NFT | false |
| script | Для смарт-ассета — скомпилированный скрипт ассета, до 8192 байт, в кодировке base64.<br>При значении null — выпускается токен без скрипта.<br>Если при выпуске к ассету не прикреплен скрипт, в дальнейшем его нельзя сделать смарт-ассетом.| null |
<br>

<!-- Reissue transaction = Транзакция довыпуска -->
#### `Reissue transaction` ####
Транзакция довыпуска увеличивает количество [токена](https://docs.waves.tech/ru/blockchain/token/) на блокчейне и/или запрещает его довыпуск.<br>
Подробнее про [транзакцию довыпуска](https://docs.waves.tech/ru/blockchain/transaction-type/reissue-transaction#%D0%BA%D0%BE%D0%BC%D0%B8%D1%81%D1%81%D0%B8%D1%8F-%D0%B7%D0%B0-%D1%82%D1%80%D0%B0%D0%BD%D0%B7%D0%B0%D0%BA%D1%86%D0%B8%D1%8E).

Запустим транзакцию создания [тестового токена](#issue-transaction).<br>
Далее создадим функцию довыпуска токена, ссылаясь на его assetId.

```Java
// Необходимые импортирования
import com.wavesplatform.wavesj.Node;
import com.wavesplatform.transactions.common.Amount;
import com.wavesplatform.transactions.common.AssetId;
import com.wavesplatform.transactions.common.ChainId;
import com.wavesplatform.transactions.IssueTransaction;
import com.wavesplatform.transactions.ReissueTransaction;
import com.wavesplatform.wavesj.info.IssueTransactionInfo;
import com.wavesplatform.wavesj.info.ReissueTransactionInfo;

// Реализация через builder
AssetId assetId = node.waitForTransaction(node.broadcast(
        IssueTransaction.builder("Asset", 1000, 2).getSignedWith(alice)).id(),
        IssueTransactionInfo.class).tx().assetId();
ReissueTransaction tx = ReissueTransaction.builder(Amount.of(1000, assetId)).getSignedWith(alice);


// Реализация через constructor
IssueTransaction itx = new IssueTransaction(alice.publicKey(),"Asset","",1000,2,true,Base64String.empty())
        .addProof(alice);
AssetId assetId = node.waitForTransaction(node.broadcast(itx).id(), IssueTransactionInfo.class).itx().assetId();
ReissueTransaction tx = new ReissueTransaction(alice.publicKey(),Amount.of(1000, assetId),true)
        .addProof(alice);

// Отправляем транзакцию на ноду и выводим информацию о ней
node.waitForTransaction(node.broadcast(tx).id());
ReissueTransactionInfo txInfo = node.getTransactionInfo(tx.id(), ReissueTransactionInfo.class);

System.out.println("type:" + txInfo.tx().type());
System.out.println("id:" + txInfo.tx().id());
System.out.println("fee:" + txInfo.tx().fee().value());
System.out.println("feeAssetId:" + txInfo.tx().fee().assetId().encoded());
System.out.println("timestamp:" + txInfo.tx().timestamp());
System.out.println("version:" + txInfo.tx().version());
System.out.println("chainId:" + txInfo.tx().chainId());
System.out.println("sender:" + txInfo.tx().sender().address().encoded());
System.out.println("senderPublicKey:" + txInfo.tx().sender().encoded());
System.out.println("proofs:" + txInfo.tx().proofs());
System.out.println("assetId:" + txInfo.tx().amount().assetId().encoded());
System.out.println("quantity:" + txInfo.tx().amount().value());
System.out.println("reissuable:" + txInfo.tx().reissuable());
System.out.println("height:" + txInfo.height());
System.out.println("applicationStatus:" + txInfo.applicationStatus());
```
**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
| assetId <span style="color:red">(required)</span> | ID токена в кодировке base58.| GSFk5Ziwx33g8KuMyh6wYerxJcHXdcGgXFiBYXH58AE6 |
| quantity <span style="color:red">(required)</span> | Количество токена к довыпуску.<br>Целое число, выраженное в минимальных неделимых единицах («копейках») токена.<br>Итоговое количество токена в результате довыпуска не должно превышать<br> 9 223 372 036 854 775 807| 1000|
| reissuable <span style="color:red">(required)</span> | Флаг возможности довыпуска. | true |
<br>

<!-- Burn transaction = Транзакция сжигания токена -->
#### `Burn transaction` ####
Транзакция сжигания токена уменьшает количество [токена](https://docs.waves.tech/ru/blockchain/token/) на аккаунте отправителя.<br>
Также, количество токена уменьшается на данную величину на блокчейне.<br>
Подробнее о [транзакции сжигания токена](https://docs.waves.tech/ru/blockchain/transaction-type/burn-transaction).


Запустим транзакцию создания [тестового токена](#issue-transaction).<br>
Произведем сжигание токена, ссылаясь на его assetId.

```Java
// Необходимые импортирования
import com.wavesplatform.transactions.common.Amount;
import com.wavesplatform.transactions.common.AssetId;
import com.wavesplatform.transactions.common.Base64String;
import com.wavesplatform.wavesj.info.BurnTransactionInfo;
import com.wavesplatform.wavesj.info.IssueTransactionInfo;
import com.wavesplatform.transactions.BurnTransaction;
import com.wavesplatform.transactions.IssueTransaction;

// Реализация через builder
AssetId assetId = node.waitForTransaction(node.broadcast(IssueTransaction.builder("Asset", 1000, 2)
        .getSignedWith(alice)).id(), IssueTransactionInfo.class).tx().assetId();
BurnTransaction tx = BurnTransaction.builder(Amount.of(100, assetId)).getSignedWith(alice);

// Реализация через сonstructor
IssueTransaction tx = new IssueTransaction(alice.publicKey(),"Asset","",1000,2,true, Base64String.empty())
        .addProof(alice);
AssetId assetId = node.waitForTransaction(node.broadcast(tx).id(), IssueTransactionInfo.class).tx().assetId();
BurnTransaction tx = new BurnTransaction(alice.publicKey(),new Amount(100, assetId)).addProof(alice);

// Отправка транзакции на ноду и вывод на экране информации о ней
node.waitForTransaction(node.broadcast(tx).id());
BurnTransactionInfo txInfo = node.getTransactionInfo(tx.id(), BurnTransactionInfo.class);
System.out.println("type:" + txInfo.tx().type());
System.out.println("id:" + txInfo.tx().id());
System.out.println("fee:" + txInfo.tx().fee().value());
System.out.println("feeAssetId:" + txInfo.tx().fee().assetId().encoded());
System.out.println("timestamp:" + txInfo.tx().timestamp());
System.out.println("version:" + txInfo.tx().version());
System.out.println("chainId:" + txInfo.tx().chainId());
System.out.println("sender:" + txInfo.tx().sender().address().encoded());
System.out.println("senderPublicKey:" + txInfo.tx().sender().encoded());
System.out.println("proofs:" + txInfo.tx().proofs());
System.out.println("assetId:" + txInfo.tx().amount().assetId().encoded());
System.out.println("amount:" + txInfo.tx().amount().value());
System.out.println("height:" + txInfo.height());
System.out.println("applicationStatus:" + txInfo.applicationStatus());      

```
**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
| amount <span style="color:red">(required)</span> | Количество сжигаемого токена.<br>Целое число, выраженное в минимальных неделимых единицах («копейках») токена. | 100 |
| assetId <span style="color:red">(required)</span> | ID сжигаемого токена в кодировке base58 | GSFk5Ziwx33g8KuMyh6wYerxJcHXdcGgXFiBYXH58AE6 
<br>

<!-- Set asset script = Транзакция установки скрипта ассета -->
#### `Set asset script transaction` ####
Транзакция установки скрипта ассета изменяет скрипт ассета.<br> 
Подробнее о [транзакции установки скрипта ассета]().

Трансформируем написанный скрипт в кодировку base64.<br>
Запустим транзакцию создания [тестового токена](#issue-transaction).<br>
Установим скрипт в кодировке base64 на токен.

```Java
// Необходимые импортирования
import com.wavesplatform.transactions.common.AssetId;
import com.wavesplatform.transactions.common.Base64String;
import com.wavesplatform.transactions.IssueTransaction;
import com.wavesplatform.transactions.SetAssetScriptTransaction;
import com.wavesplatform.wavesj.info.IssueTransactionInfo;
import com.wavesplatform.wavesj.info.TransactionInfo;
import com.wavesplatform.wavesj.info.SetAssetScriptTransactionInfo;

// Реализация через builder
Base64String script = node.compileScript("{-# SCRIPT_TYPE ASSET #-} true").script();
AssetId assetId = node.waitForTransaction(node.broadcast(IssueTransaction.builder("Asset", 1000, 2)
        .script(script).getSignedWith(alice)).id(), IssueTransactionInfo.class).tx().assetId();
SetAssetScriptTransaction tx = SetAssetScriptTransaction.builder(assetId, script).getSignedWith(alice);


// Реализация через сonstructor
Base64String script = node.compileScript("{-# SCRIPT_TYPE ASSET #-} true").script();
IssueTransaction tx = new IssueTransaction(
    alice.publicKey(),"Asset","",1000,2,true,Base64String.empty())
    .addProof(alice);
AssetId assetId = node.waitForTransaction(node.broadcast(tx).id(), IssueTransactionInfo.class).tx().assetId();
SetAssetScriptTransaction tx = new SetAssetScriptTransaction(alice.publicKey(), assetId, script).addProof(alice);

// Отправка транзакции на ноду и вывод на экране информации о ней
node.waitForTransaction(node.broadcast(tx).id());
TransactionInfo commonInfo = node.getTransactionInfo(tx.id());
SetAssetScriptTransactionInfo txInfo = node.getTransactionInfo(tx.id(), SetAssetScriptTransactionInfo.class);

System.out.println("type:" + txInfo.tx().type());
System.out.println("id:" + txInfo.tx().id());
System.out.println("fee:" + txInfo.tx().fee().value());
System.out.println("feeAssetId:" + txInfo.tx().fee().assetId().encoded());
System.out.println("timestamp:" + txInfo.tx().timestamp());
System.out.println("version:" + txInfo.tx().version());
System.out.println("chainId:" + txInfo.tx().chainId());
System.out.println("sender:" + txInfo.tx().sender().address().encoded());
System.out.println("senderPublicKey:" + txInfo.tx().sender().encoded());
System.out.println("proofs:" + txInfo.tx().proofs());
System.out.println("assetId:" + txInfo.tx().assetId().encoded());
System.out.println("script:" + txInfo.tx().script().encoded());
System.out.println("height:" + txInfo.height());
System.out.println("applicationStatus:" + txInfo.applicationStatus());

```
**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
| assetId <span style="color:red">(required)</span> | ID токена в кодировке base58. | 7qJUQFxniMQx45wk12UdZwknEW9cDgvfoHuAvwDNVjYv |
| script <span style="color:red">(required)</span> | Cкомпилированный скрипт ассета, до 8192 байт, в кодировке base64. | base64:AQa3b8tH |

<br>

<!-- Update asset info transaction = Транзакция обновления информации ассета -->
#### `Update asset info transaction` ####
Транзакция обновления информации ассета изменяет название и описание [токена](https://docs.waves.tech/ru/blockchain/token/).<br>
Подробнее о [транзакции обновления информации ассета](https://docs.waves.tech/ru/blockchain/transaction-type/update-asset-info-transaction).


Запустим транзакцию создания [тестового токена](#issue-transaction).
Подождем добавления 2 новых блоков блокчейна.
Проведем транзакцию обновления информации ассета.

```Java
// Необходимые импортирования
import com.wavesplatform.transactions.UpdateAssetInfoTransaction;
import com.wavesplatform.wavesj.info.IssueTransactionInfo;
import com.wavesplatform.transactions.IssueTransaction;
import com.wavesplatform.transactions.common.AssetId;
import com.wavesplatform.transactions.common.Base64String;

// Реализация через builder
AssetId assetId = node.waitForTransaction(node.broadcast(
        IssueTransaction.builder("Asset", 1000, 2).getSignedWith(alice)).id(),
        IssueTransactionInfo.class).tx().assetId();
node.waitBlocks(2);
UpdateAssetInfoTransaction tx = UpdateAssetInfoTransaction
        .builder(assetId, "New Asset", "New description").getSignedWith(alice);

// Реализация через сonstructor
IssueTransaction tx = new IssueTransaction(alice.publicKey(),"Asset","",1000,2,true, Base64String.empty())
        .addProof(alice);
AssetId assetId = node.waitForTransaction(node.broadcast(tx).id(), IssueTransactionInfo.class).tx().assetId();
node.waitBlocks(2);
        
UpdateAssetInfoTransaction tx = new UpdateAssetInfoTransaction(alice.publicKey(),assetId,"New Asset","New description")
    .addProof(alice);

// Отправляем транзакцию на ноду и выводим информацию о ней
node.waitForTransaction(node.broadcast(tx).id());
System.out.println("type:" + txInfo.tx().type());
System.out.println("id:" + txInfo.tx().id());
System.out.println("fee:" + txInfo.tx().fee().value());
System.out.println("feeAssetId:" + txInfo.tx().fee().assetId().encoded());
System.out.println("timestamp:" + txInfo.tx().timestamp());
System.out.println("version:" + txInfo.tx().version());
System.out.println("chainId:" + txInfo.tx().chainId());
System.out.println("sender:" + txInfo.tx().sender().address().encoded());
System.out.println("senderPublicKey:" + txInfo.tx().sender().encoded());
System.out.println("proofs:" + txInfo.tx().proofs());
System.out.println("assetId:" + txInfo.tx().assetId().encoded());
System.out.println("name:" + txInfo.tx().name());
System.out.println("description:" + txInfo.tx().description());
System.out.println("height:" + txInfo.height());
System.out.println("applicationStatus:" + txInfo.applicationStatus());
```

**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
| name <span style="color:red">(required)</span> | Название токена. От 4 до 16 байт. | New asset name |
| description <span style="color:red">(required)</span> | Описание токена. От 0 до 1000 байт. | Random phrase |
| assetId <span style="color:red">(required)</span> | ID токена в кодировке base58. | syXBywr2HVY7wxqkaci1jKY73KMpoLh46cp1peJAZNJ |

<br>

---

**Использование** <br>
<!-- Transfer transaction = Транзакция перевода -->
#### `Transfer transaction` ####
Транзакция перевода выполняет перевод указанного количества [токена](https://docs.waves.tech/ru/blockchain/token/) на другой аккаунт.<br>
Подробнее о [транзакции перевода](https://docs.waves.tech/ru/blockchain/transaction-type/transfer-transaction).

Задаем [адрес получателя](#работа-с-аккаунтом) (переменная bob) и ключевые данные транзакции (ID ассета, сумма).<br>
Транзакция подписывается приватным ключём пользователя alice.

```Java
// Необходимые импортирования
import com.wavesplatform.wavesj.info.TransferTransactionInfo;
import com.wavesplatform.transactions.TransferTransaction;
import com.wavesplatform.transactions.common.Amount;

// Реализация через builder
TransferTransaction tx = TransferTransaction.builder(bob.address(), Amount.of(1000)).getSignedWith(alice);

// Реализация через сonstructor
TransferTransaction tx = new TransferTransaction(alice.publicKey(), bob.address(),Amount.of(1000),null).addProof(alice);

// Отправляем транзакцию на ноду и выводим информацию о ней 
node.waitForTransaction(node.broadcast(tx).id());
TransferTransactionInfo txInfo = node.getTransactionInfo(tx.id(), TransferTransactionInfo.class);

System.out.println("type:" + txInfo.tx().type());
System.out.println("id:" + txInfo.tx().id());
System.out.println("fee:" + txInfo.tx().fee().value());
System.out.println("feeAssetId:" + txInfo.tx().fee().assetId().encoded());
System.out.println("timestamp:" + txInfo.tx().timestamp());
System.out.println("version:" + txInfo.tx().version());
System.out.println("chainId:" + txInfo.tx().chainId());
System.out.println("sender:" + txInfo.tx().sender().address().encoded());
System.out.println("senderPublicKey:" + txInfo.tx().sender().encoded());
System.out.println("proofs:" + txInfo.tx().proofs());
System.out.println("recipient:" + txInfo.tx().recipient());
System.out.println("assetId:" + txInfo.tx().amount().assetId().encoded());
System.out.println("amount:" + txInfo.tx().amount().value());
System.out.println("attachment:" + txInfo.tx().attachment().encoded());
System.out.println("height:" + txInfo.height());
System.out.println("applicationStatus:" + txInfo.applicationStatus());

```
**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
| assetId | ID переводимого токена в кодировке base58.<br>null — перевод в WAVES. | 7uncmN7dZfV3fYVvNdYTngrrbamPYMgwpDnYG1bGy6nA |
| amount <span style="color:red">(required)| Количество токена к переводу.<br>Целое число, выраженное в минимальных неделимых единицах («копейках») токена.| 1000 |
| recipient <span style="color:red">(required)| Адрес получателя в кодировке base58 или [псевдоним](https://docs.waves.tech/ru/blockchain/account/alias) адреса c префиксом alias:<байт_сети>:.<br>Например alias:T:merry (см.[Байт сети)](https://docs.waves.tech/ru/blockchain/blockchain-network/#%D0%B1%D0%B0%D0%B9%D1%82-%D1%81%D0%B5%D1%82%D0%B8) | 3PFmoN5YLoPNsL4cmNGkRxbUKrUVntwyAhf |
| attachment | 	Произвольные данные (обычно комментарий к транзакции), до 140 байт, в кодировке base58 | 3vrgtyozxuY88J9RqMBBAci2UzAq9DBMFTpMWLPzMygGeSWnD7k | 

<br>

<!-- Set script transaction =  Транзакция установки скрипта -->
#### `Set script transaction` ####
Транзакция установки скрипта прикрепляет [dApp-скрипт](https://docs.waves.tech/ru/ride/script/script-types/dapp-script) или [скрипт аккаунта](https://docs.waves.tech/ru/ride/script/script-types/account-script) к аккаунту отправителя.<br>
Подробнее о [транзакции установки скрипта](https://docs.waves.tech/ru/blockchain/transaction-type/set-script-transaction).



```Java
// Необходимые импортирования
// Реализация через builder
// Реализация через сonstructor
```
**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
|  <span style="color:red">(required)</span> | |  |
<!-- Invoke script transaction = Транзакция вызова скрипта -->
#### `Invoke script transaction` ####
Транзакция вызова скрипта выполняет вызов функции [dApp](https://docs.waves.tech/ru/blockchain/account/dapp).<br>
Подробнее о [транзакции вызова скрипта](https://docs.waves.tech/ru/blockchain/transaction-type/invoke-script-transaction).<br>
Подробнее о [dApp и о вызове скрипта](https://docs.waves.tech/ru/building-apps/smart-contracts/what-is-a-dapp).


```Java
// Необходимые импортирования
// Реализация через builder
// Реализация через сonstructor
```
**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
|  <span style="color:red">(required)</span> | |  |
<!-- Exchange transaction = Транзакция обмена -->
#### `Exchange transaction` ####
Транзакция обмена обменивает два различных токена между двумя аккаунтами.<br>
Подробнее о [транзакции обмена](https://docs.waves.tech/ru/blockchain/transaction-type/exchange-transaction).


```Java
// Необходимые импортирования
// Реализация через builder
// Реализация через сonstructor
```
**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
|  <span style="color:red">(required)</span> | |  |
<!-- Mass transfer transaction = Транзакция массового перевода  -->
#### `Mass transfer transaction` ####
Транзакция массового перевода выполняет перевод токена нескольким получателям (от 1 до 100).<br>
Подробнее о [транзакции массового перевода](https://docs.waves.tech/ru/blockchain/transaction-type/mass-transfer-transaction).


```Java
// Необходимые импортирования
// Реализация через builder
// Реализация через сonstructor
```
**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
|  <span style="color:red">(required)</span> | |  |
<!-- Data transaction = Транзакция данных -->
#### `Data transaction` ####
Транзакция данных добавляет, удаляет или изменяет записи в [хранилище данных аккаунта](https://docs.waves.tech/ru/blockchain/account/account-data-storage) отправителя.<br>
Подробнее о [транзакции данных](https://docs.waves.tech/ru/blockchain/transaction-type/data-transaction).


```Java
// Необходимые импортирования
// Реализация через builder
// Реализация через сonstructor
```
**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
|  <span style="color:red">(required)</span> | |  |
<!-- Create alias transaction = Транзакция создания псевдонима -->
#### `Create alias transaction` ####
Транзакция создания псевдонима создает [псевдоним](https://docs.waves.tech/ru/blockchain/account/alias) для адреса отправителя.<br>
Подробнее о [транзакции создания псевдонима](https://docs.waves.tech/ru/blockchain/transaction-type/create-alias-transaction).

<br>

---

**Сеть** <br>

<!-- Lease transaction = Транзакция лизинга -->
#### `Lease transaction` ####
Транзакция лизинга передает WAVES в лизинг другому аккаунту.<br>
Подробнее о [транзакции лизинга](https://docs.waves.tech/ru/blockchain/transaction-type/lease-transaction).


```Java
// Необходимые импортирования
// Реализация через builder
// Реализация через сonstructor
```
**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
|  <span style="color:red">(required)</span> | |  |
<!-- Lease cancel transaction = Транзакция закрытия лизинга -->
#### `Lease cancel transaction` ####
Транзакция закрытия лизинга прекращает [лизинг](#lease-transaction).<br>
Подробнее о [транзакции закрытия лизинга](https://docs.waves.tech/ru/blockchain/transaction-type/lease-cancel-transaction).


```Java
// Необходимые импортирования
// Реализация через builder
// Реализация через сonstructor
```
**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
|  <span style="color:red">(required)</span> | |  |
<!-- Sponsor fee transaction = Транзакция спонсирования -->
#### `Sponsor fee transaction` ####
Транзакция спонсирования устанавливает или отменяет спонсирование.<br>
Подробнее о [транзакции спонсирования](https://docs.waves.tech/ru/blockchain/transaction-type/sponsor-fee-transaction).


```Java
// Необходимые импортирования
// Реализация через builder
// Реализация через сonstructor
```
**Описание аргументов**
| Наименование поля | Описание | Пример |
| ----------- | ----------- | ----------- |
|  <span style="color:red">(required)</span> | |  |

<br>

### Работа с нодой ###
