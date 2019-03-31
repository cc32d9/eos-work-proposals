# EOS History database: Project Sponsors

cc32d9@gmail.com

## List of sponsors

15 teams have confirmed their sponsorship for the project. They are
listed below in the order of sending their confirmations.

12 sponsors are the Block Producers or BP Candidates for EOS
mainnet. Their BP account names are listed, accordingly.

Two EOS sister chains are contributing to the project: WORBLI launching
their own network, and GoodBlock as a BP candidate for Telos.

* EOS Tribe (`eostribeprod`) https://eostribe.io/

* EOSMetal (`eosmetaliobp`) https://eosmetal.io/

* WORBLI https://worbli.io/

* GoodBlock/Telos https://goodblock.io/

* EOS Rio eosriobrazil (`eosriobrazil`) https://eosrio.io/

* EOS Cafe Block (`eoscafeblock`) https://eoscafeblock.com/

* EOS New York (`eosnewyorkio`) https://eosnewyork.io

* EOS Nation (`eosnationftw`) https://eosnation.io

* EOS Dublin (`eosdublinwow`) https://www.eosdublin.com/

* TeamGreymass (`teamgreymass`) https://greymass.com/

* EOSGen (`eosgenblockp`) https://www.genesis-mining.com/eos

* EOS Asia (`eosasia11111`) https://eosasia.one

* Attic Lab (`atticlabeosb`) https://atticlab.net/eos/

* EOS Authortity (`eosauthority`)  https://eosauthority.com/

* EOS Lynx http://eoslynx.com/


## Funds deposit

The whole budget of 5610 EOS is split into 15 parts of 374 EOS each, and
will be deposited on the EOS account `cc32dninewp1`. Active and owner
permissions for this account are set to the sponsor active keys,
requiring 3 signatures out of 15.


## Voting for Block Producers

We encourage everyone to support the project by voting for the
sponsors. Instructions for `cleos` follow:

```
alias mcleos='cleos -u http://api2.eosmetal.io'
VOTELIST="eostribeprod eosmetaliobp eosriobrazil eoscafeblock \
eosnewyorkio eosnationftw eosdublinwow teamgreymass \
eosgenblockp eosasia11111 atticlabeosb eosauthority"

mcleos system voteproducer prods MYACCOUNT $VOTELIST
```

