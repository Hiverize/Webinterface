<template>
    <div>
        <h1>{{ $t('wlan.title') }}</h1>

        <ConfigForm v-model="wlan_config"
                    config_url="/api/config/networking/wlan">
            <p>
                <label for="enabled">{{ $t('general.enabled') }}:</label>
                <input type="checkbox"
                    name="enabled"
                    v-model="wlan_config.enabled">
            </p>
            <p>
                <label for="antenna_external">{{ $t('wlan.antenna_external') }}:</label>
                <input type="checkbox"
                    name="antenna_external"
                    v-model="wlan_config.antenna_external">
            </p>
            <p>
                <label for="pin_dout">{{ $t('wlan.antenna_pin') }}:</label>
                <ConfigPinSelect name="pin_dout"
                                 v-model="wlan_config.antenna_pin" />
            </p>
            <p v-if="ssid_visible">
                <label for="ssid">{{ $t('networking_general.ssid') }}:</label>
                <select name="ssid"
                        v-model="wlan_config.ssid"
                        @change="update_encryption()">
                    <option disabled
                            value="">
                        {{ $t('wlan.ssid_select') }}
                    </option>
                    <option v-for="ap in wlan_config.available"
                            :value="ap.ssid"
                            :key="ap.bssid">
                        {{ ap.ssid }}
                    </option>
                </select>
            </p>
            <p v-if="ssid_visible" id="ssid_not_listed">
                <label for="ssid_visible">
                </label>
                <button @click="ssid_visible = false">{{ $t('wlan.ssid_not_listed') }}</button>
            </p>
            <p v-else>
                <label for="ssid">{{ $t('wlan.ssid') }}:</label>
                <input type="text"
                       name="ssid"
                       v-model="wlan_config.ssid">
            </p>
            <p>
                <label for="encryption">{{ $t('networking_general.encryption') }}:</label>
                <select name="encryption"
                        v-model="wlan_config.encryption">
                    <option value="">None</option>
                    <option value="1">WEP</option>
                    <option value="2">WPA</option>
                    <option value="3">WPA2</option>
                    <option value="5">WPA2 ENT</option>
                </select>
            </p>
            <p>
                <label for="password">{{ $t('networking_general.password') }}:</label>
                <input type="password"
                       name="password"
                       v-model="wlan_config.password">
            </p>
            <p>
                <label for="ifconfig">{{ $t('wlan.ip_address') }}:</label>
                <span>
                    <input type="radio"
                        name="ifconfig"
                        value="dhcp"
                        v-model="wlan_config.ifconfig"> DHCP
                    <input type="radio"
                        name="ifconfig"
                        value="static"
                        v-model="wlan_config.ifconfig"> Static
                </span>
            </p>
            <div id="ipv4_static"
                v-if="wlan_config.ifconfig === 'static'">
                <p>
                    <label for="ipaddress">{{ $t('wlan.address') }}:</label>
                    <input type="text"
                           name="ipaddress"
                           v-model="wlan_config.ipaddress">
                </p>
                <p>
                    <label for="subnet">{{ $t('wlan.subnet') }}:</label>
                    <input type="text"
                           name="subnet"
                           v-model="wlan_config.subnet">
                </p>
                <p>
                    <label for="gateway">{{ $t('wlan.gateway') }}:</label>
                    <input type="text"
                           name="gateway"
                           v-model="wlan_config.gateway">
                </p>
                <p>
                    <label for="dns">{{ $t('wlan.dns') }}:</label>
                    <input type="text"
                           name="dns"
                           v-model="wlan_config.dns">
                </p>
            </div>
        </ConfigForm>
    </div>
</template>

<script>

import ConfigForm from '@/components/ConfigForm'
import ConfigPinSelect from '@/components/ConfigPinSelect'

export default {
    name: 'NetworkingWLan',
    data() {
        return {
            ssid_visible: true,
            wlan_config: {
                antenna_pin: '',
                available: [],
                ssid: '',
                encryption: '',
                password: '',
                ifconfig: 'dhcp',
                ipaddress: '',
                subnet: '',
                gateway: '',
                dns: ''
            }
        }
    },
    methods: {
        update_encryption() {
            let ap = this.wlan_config.available.filter(ap => {
                return ap.ssid === this.wlan_config.ssid;
            });
            if (ap.length > 0) {
                this.$set(this.wlan_config, 'encryption', ap[0].sec);
            }
        }
    },
    components: {
        ConfigForm,
        ConfigPinSelect
    }
}

</script>

<style>
#ssid_not_listed {
    margin-top: 0em !important;
}
</style>
