# WavesJ (Java Library)

RU | [EN](GOOGLE.RU)

## Описание библиотеки ##
Библиотека, написанная на Java, позволяющая взаимодействовать с блокчейном Waves.<br>
Она предоставляет собой набор готовых инструментов, позволяющих работать c:
- Аккаунтами;
- Транзакциями;
- Нодой Waves.

## Содержание ##
  - [Описание библиотеки](#описание-библиотеки)
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
   - Подпись биржевых ордеров и отправка на матчер. 
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
```
В следующей последовательности мы:
1. Генерируем случайный seed;
2. Создаем приватный ключ из seed для пользователя alice;
3. Создаем публичный ключ из приватного ключа;
4. Формируем адрес кошелька из публичного ключа.
```Java
String seed = Crypto.getRandomSeedPhrase();
PrivateKey alice = PrivateKey.fromSeed(seed);
PublicKey publicKey = PublicKey.from(alice);
Address address = Address.from(publicKey);
```
### Работа с транзакциями ###
На платформе Waves возможно проведение следующих типов транзакций:
   1. Токенизация:
       - 
       <details>
       <summary>Транзакция выпуска</summary>
        ```Java
        String seed = Crypto.getRandomSeedPhrase();
        ```
       </details>
       
       - [Транзакция выпуска](https://docs.waves.tech/ru/blockchain/transaction-type/issue-transaction)
       - [Транзакция довыпуска](https://docs.waves.tech/ru/blockchain/transaction-type/reissue-transaction)
       - [Транзакция установки скрипта ассета](https://docs.waves.tech/ru/blockchain/transaction-type/set-asset-script-transaction)
       - [Транзакция обновления информации ассета](https://docs.waves.tech/ru/blockchain/transaction-type/update-asset-info-transaction)
       - [Транзакция сжигания токена](https://docs.waves.tech/ru/blockchain/transaction-type/burn-transaction)
   2. Использование
       - [Транзакция перевода](https://docs.waves.tech/ru/blockchain/transaction-type/transfer-transaction)
       - [Транзакция обмена](https://docs.waves.tech/ru/blockchain/transaction-type/exchange-transaction)
       - [Транзакция массового перевода](https://docs.waves.tech/ru/blockchain/transaction-type/mass-transfer-transaction)
       - [Транзакция установки скрипта](https://docs.waves.tech/ru/blockchain/transaction-type/set-script-transaction)
       - [Транзакция вызова скрипта](https://docs.waves.tech/ru/blockchain/transaction-type/invoke-script-transaction)
       - [Транзакция данных](https://docs.waves.tech/ru/blockchain/transaction-type/data-transaction)
       - [Транзакция создания псевдонима](https://docs.waves.tech/ru/blockchain/transaction-type/create-alias-transaction)
       - [Транзакция в формате Ethereum](https://docs.waves.tech/ru/blockchain/transaction-type/ethereum-transaction)
   3. Сеть
       - [Транзакция лизинга](https://docs.waves.tech/ru/blockchain/transaction-type/lease-transaction)
       - [Транзакция закрытия лизинга](https://docs.waves.tech/ru/blockchain/transaction-type/lease-cancel-transaction)
       - [Транзакция спонсирования](https://docs.waves.tech/ru/blockchain/transaction-type/sponsor-fee-transaction)
     
Токенизация:    
- IssueTransaction = Транзакция выпуска
- Reissue transaction = Транзакция довыпуска
- Set asset script = Транзакция установки скрипта ассета
- Burn transaction = Транзакция сжигания токена

Использование
- Transfer transaction   
- Exchange transaction
- Mass transfer transaction
- Data transaction
- Create alias transaction

Сеть:
- Lease transaction
- Lease cancel transaction
- Sponsor fee transaction

### Работа с нодой ###
