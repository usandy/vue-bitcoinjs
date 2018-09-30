<template>
    <el-container >
        <el-container style="margin-left:100px;margin-right: 100px">
            <el-header style="text-align: left; font-size: 24px">
                <span>Mnemonic</span>
            </el-header>

            <el-main >
                <el-card class="box-card">
                    <div slot="header" class="clearfix">
                        <span>BIP39 Mnemonic</span>
                    </div>
                    <div class="text item">
                        <el-input v-model="mnemonic"></el-input>
                    </div>
                </el-card>
                <el-card class="box-card">
                    <div slot="header" class="clearfix">
                        <span>Bip39 Seed</span>
                    </div>
                    <div class="text item">
                        {{mnemonicToSeedHex}}
                    </div>
                </el-card>
                <el-card class="box-card">
                    <div slot="header" class="clearfix">
                        <span>RootKey Seed</span>
                    </div>
                    <div class="text item">
                        {{rootKey.toBase58()}}
                    </div>
                </el-card>
                <el-card class="box-card">
                    <div slot="header" class="clearfix">
                        <span>BIP39 Passphrase </span>
                    </div>
                    <div class="text item">
                        <el-input v-model="passphrase"></el-input>
                    </div>
                </el-card>
                <el-card class="box-card">
                    <div slot="header" class="clearfix">
                        <span>Words count</span>
                    </div>
                    <div class="text item">
                        <el-select v-model="countWords" placeholder="Select">
                            <el-option
                                    v-for="item in strengthWords"
                                    :key="item.value"
                                    :label="item.label"
                                    :value="item.value"
                                    :disabled="item.disabled">
                            </el-option>
                        </el-select>
                    </div>
                    <br>
                    <div class="text item">

                        <el-button @click="testBitcoin">Regenerate Mnemonic</el-button>
                    </div>
                </el-card>
                <!--<el-table :data="tableData">-->
                    <!--<el-table-column prop="date" label="Date" width="140">-->
                    <!--</el-table-column>-->
                    <!--<el-table-column prop="name" label="Name" width="120">-->
                    <!--</el-table-column>-->
                    <!--<el-table-column prop="address" label="Address">-->
                    <!--</el-table-column>-->
                <!--</el-table>-->
            </el-main>
            <el-container>
                <el-header style="text-align: left; font-size: 24px">
                    <span>Derived Path</span>
                </el-header>

                <el-tabs v-model="activeModel" @tab-click="handleClick">
                    <el-tab-pane label="BIP32" name="BIP32">
                        <el-card class="box-card">
                            <div slot="header" class="clearfix">
                                <span>Purpose</span>
                            </div>
                            <div class="text item">
                                {{purpose}}
                            </div>
                        </el-card>
                        <el-card class="box-card">
                            <div slot="header" class="clearfix">
                                <span>BIP39 Mnemonic</span>
                            </div>
                            <div class="text item">
                                <el-input v-model="mnemonic"></el-input>
                            </div>
                        </el-card>
                    </el-tab-pane>
                    <el-tab-pane label="BIP44" name="BIP44">
                        <el-card class="box-card">
                            <div slot="header" class="clearfix">
                                <span>BIP44</span>
                            </div>
                            <span class="demo-input-label">Purpose</span>
                            <div class="text item">
                                {{purpose}}
                            </div>
                            <span class="demo-input-label">Coin</span>
                            <div class="text item">
                                {{coin}}
                            </div>

                            <span class="demo-input-label">Account</span>
                            <div class="text item">
                                <el-input v-model="account"></el-input>
                            </div>


                            <span class="demo-input-label">External / Internal</span>
                            <div class="text item">
                                <el-input v-model="external"></el-input>
                            </div>
                            <br>
                            <el-card class="box-card">
                                <div slot="header" class="clearfix">
                                    <span>Account Extended Public Key</span>
                                </div>
                                <div class="text item">
                                    {{accountextxpub}}
                                </div>
                            </el-card>
                            <el-card class="box-card">
                                <div slot="header" class="clearfix">
                                    <span>Account Extended Private Key</span>
                                </div>
                                <div class="text item">
                                    {{accountextxprv}}
                                </div>
                            </el-card>
                            <el-card class="box-card">
                                <div slot="header" class="clearfix">
                                    <span>BIP32 Derivation Path</span>
                                </div>
                                <div class="text item">
                                    {{getDerivationPath()}}
                                </div>
                            </el-card>
                            <el-card class="box-card">
                                <div slot="header" class="clearfix">
                                    <span>BIP32 Extended Private Key</span>
                                </div>
                                <div class="text item">
                                    {{extendedPrivateKey}}
                                </div>
                            </el-card>
                            <el-card class="box-card">
                                <div slot="header" class="clearfix">
                                    <span>BIP32 Extended Public Key</span>
                                </div>
                                <div class="text item">
                                    {{extendedPublicKey}}
                                </div>
                            </el-card>
                        </el-card>
                    </el-tab-pane>
                    <el-tab-pane label="BIP49" name="BIP49">BIP 49</el-tab-pane>
                    <el-tab-pane label="BIP84" name="BIP84">BIP 84</el-tab-pane>
                    <el-tab-pane label="BIP141" name="BIP141">BIP 141</el-tab-pane>
                </el-tabs>

            </el-container>
        </el-container>

    </el-container>
</template>

<script>
  import bitcoin from 'bitcoinjs-lib';
  import bip39 from 'bip39';
  import bip32 from 'bip32'
export default {

    data(){
        const item = {
            date: '2016-05-02',
            name: 'Tom',
            address: 'No. 189, Grove St, Los Angeles'
        };
        const strengthWords = [
            {
                value: 12,
                label: 12
            }, {
                value: 15,
                label: 15
            },
            {
                value: 18,
                label: 18
            },
            {
                value: 21,
                label: 21
            },
            {
                value: 24,
                label: 24
            }
        ]

        return{
          mnemonic: bip39.generateMnemonic(15 / 3 * 32),
            node:'',
            string:'',
            restored:'',
            passphrase:'',
            countWords: 15,
            strengthWords: strengthWords,
            activeModel: 'BIP44',
            network: bitcoin.networks.bitcoin,
            purpose: 44,
            coin: 0,
            account:0,
            external:0
        }
    },
    methods:{
        testBitcoin: function () {
            this.mnemonic = bip39.generateMnemonic(this.wordsCountComp);
            console.log(this.getDerivationPath(true));
        },
        getAddress: function(node, network) {
            return bitcoin.payments.p2pkh({ pubkey: node.publicKey, network }).address
        },
        handleClick(tab, event) {
            console.log(tab);
            this.purpose = +tab.name.substr(3,4)
        },
        calcBip32ExtendedKey(path){

            if (!this.rootKey) {
                return this.rootKey;
            }
            let extendedKey = this.rootKey;
            // Derive the key from the path
            let pathBits = path.split("/");
            for (let i=0; i<pathBits.length; i++) {
                let bit = pathBits[i];
                let index = parseInt(bit);
                if (isNaN(index)) {
                    continue;
                }
                let hardened = bit[bit.length-1] == "'";
                let isPriv = !(extendedKey.isNeutered());
                let invalidDerivationPath = hardened && !isPriv;
                if (invalidDerivationPath) {
                    extendedKey = null;
                }
                else if (hardened) {
                    extendedKey = extendedKey.deriveHardened(index);
                }
                else {
                    extendedKey = extendedKey.derive(index);
                }
            }
            return extendedKey
        },
        getDerivationPath(withoutExternal = false){
            let path="m/";
            path += this.purpose + "'/";
            path += this.coin + "'/";
            path += this.account + "'/";
            path += withoutExternal? '' :  this.external;

            return path;
        }

    },
    mounted(){
        this.node = bip32.fromSeed(this.mnemonicToSeed);
        this.string = this.node.toBase58();
        this.restored = bip32.fromBase58(this.string);
    },
    computed:{
        mnemonicToSeed(){
            return bip39.mnemonicToSeed(this.mnemonic,this.passphrase)
        },
        mnemonicToSeedHex(){
          return   bip39.mnemonicToSeedHex(this.mnemonic,this.passphrase);
        },
        addressNode(){
          if(!this.node) return '';
          return this.getAddress(this.node, this.network);
        },
        rootKey(){
            return bip32.fromSeed(this.mnemonicToSeed,this.network)
        },
        wordsCountComp(){
            return this.countWords / 3 * 32
        },
        extendedPrivateKey(){
            return this.calcBip32ExtendedKey(this.getDerivationPath()).neutered().toBase58()
        },
        extendedPublicKey(){
            return this.calcBip32ExtendedKey(this.getDerivationPath()).toBase58()
        },
        accountextxpub(){
            return this.calcBip32ExtendedKey(this.getDerivationPath(true)).neutered().toBase58();
        },
        accountextxprv(){
            return  this.calcBip32ExtendedKey(this.getDerivationPath(true)).toBase58();
        }
    }
}
</script>

<style>
    .demo-input-label {
        display: inline-block;
        width: 130px;
    }
</style>
