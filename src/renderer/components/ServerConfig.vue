<template>
<div id='server_config'>

  <el-divider content-position="left">Local Server Configuration</el-divider>

  <div class="q-pl-md">
  <el-form :inline="true" label-width="150px" size="mini" label-position="left" @submit.native.prevent>
      <el-row :gutter="10">
          <el-col :span="10">
              <el-form-item label="Server IP">
                  <el-select v-model="server.ip" placeholder="Networkinterface" default-first-option>
                      <el-option :label="i.title" :value="i.ip" v-for="i in ifaces" :key="i.ip"></el-option>
                  </el-select>
              </el-form-item>
          </el-col>
          <el-col :span="10">
              <el-form-item label="Port">
                <el-input v-model="server.port"></el-input>
              </el-form-item>
          </el-col>
          <el-col :span="4">
              <el-button size="mini" icon="fa fa-server" style="width: calc(100% - 40px)" @click="$root.openServer()"> Server </el-button>
          </el-col>
      </el-row>

      <el-row :gutter="10">
          <el-col :span="10">
              <el-form-item label="Server App">
                  <el-select v-model="server.app" placeholder="Application" default-first-option>
                      <el-option :label="i.title" :value="i.app" :disabled="i.disabled" v-for="i in apps" :key="i.app"></el-option>
                  </el-select>
              </el-form-item>
          </el-col>
          <el-col :span="10">
              <el-form-item label="Status">
                  <el-tag size="small" style="width:100%;" :type="$helper.getServerStatusType(status)">{{ status }}</el-tag>
              </el-form-item>
          </el-col>
          <el-col :span="4">
              <el-button size="mini" icon="el-icon-refresh" @click="$root.sendServer('refresh')"></el-button>
              <el-button size="mini" icon="el-icon-switch-button" @click="$root.sendServer('toggle')"></el-button>
          </el-col>
      </el-row>


      <el-divider content-position="right">Location</el-divider>
      <el-row>
          <el-col :span="24">
              <el-form-item label="PKG Base Path" class="base_path">
                <el-input placeholder="Select your base path of your PKG's" v-model="server.base_path" disabled>
                    <el-button slot="append" icon="el-icon-edit" @click.native="enterManuallyBasePath"> Enter manually</el-button>
                    <el-button slot="append" icon="el-icon-folder" @click.native="selectBasePath"> Click here to Choose the Path</el-button>
                </el-input>
              </el-form-item>
          </el-col>
      </el-row>

      <div>
          <el-form-item label="Auto scan">
              <el-checkbox v-model="server.auto_scan_on_startup" disabled>Auto scan base path on Startup</el-checkbox>
          </el-form-item>
      </div>
      <div>
          <el-form-item label="Deepscan">
              <el-checkbox v-model="server.scan_subdir">Scan sub directories for pkg files</el-checkbox>
          </el-form-item>
      </div>

      <el-divider content-position="right">Features</el-divider>
      <div>
          <el-form-item label="URL Prefix">
              <el-checkbox v-model="server.prependFullPath"> Prefix Serving File URL with full file path, makes each file unique</el-checkbox>
          </el-form-item>
      </div>
      <div>
          <el-form-item label="Queue Scanner">
              <el-checkbox v-model="server.enableQueueScanner"> Enable Queue Scanner to automatically start the next install process on the Queue</el-checkbox>
          </el-form-item>
      </div>
      <div>
          <el-form-item label="Read SFO Header">
              <el-checkbox v-model="server.readSFOHeader" :disabled="ps4.app == 'goldhen'"> Read SFO Header of each PKG and show PKG Information </el-checkbox>
          </el-form-item>
      </div>      

  </el-form>
  </div>

  <template v-if="debug">
    <pre>Server {{ server }}</pre>
    <pre>Interfaces {{ ifaces }}</pre>
  </template>

</div>
</template>

<script>
import { get, sync } from 'vuex-pathify'
import { throttle } from 'lodash'
import { remote, ipcRenderer } from 'electron'

export default {
    name: 'ServerConfig',

    data(){ return {
        debug: false,

        ifaces: [],
        apps: [
          { title: "express", app: "express", disabled: false },
          { title: "apache", app: "apache", disabled: true },
          { title: "nginx", app: "nginx", disabled: true },
          { title: "proxy", app: "proxy", disabled: true },
          { title: "remote", app: "remote", disabled: true },
          { title: "custom", app: "custom", disabled: true },
        ]
    }},

    mounted(){
        this.loadNetworkInterfaces()
    },

    computed: {
        ps4: sync('app/ps4'),
        server: get('app/server'),
        status: get('server/status'),
    },

    watch: {
        // server: {
        //     deep: true,
        //     handler: throttle(this.save(), 2000)
        // },
        'server.ip'(){ this.save() },
        'server.port'(){ this.save() },
        'server.app'(){ this.save() },
        'server.auto_scan_on_startup'(){ this.save() },
        'server.base_path'(){
            this.save()
            this.loadFiles()
        },
        async 'server.scan_subdir'(){
            this.save()
            this.loadFiles()
        },
        async 'server.prependFullPath'(){
            this.save()
            this.loadFiles()
        },
        'server.enableQueueScanner'(){
            this.save()
        },
        'server.readSFOHeader'(){
            this.save()
            this.loadFiles()
        }
    },

    methods: {
        loadNetworkInterfaces(){
            this.ifaces = this.$helper.getNetWorkInterfaces()

            if(this.ifaces.length){
                // this.server.iface = this.ifaces[0]
            }
        },

        async selectBasePath(){
            let path = await remote.dialog.showOpenDialog({ properties: ['openDirectory'] })

            if( path && !path.canceled )
                this.server.base_path = path.filePaths[0]
        },

        loadFiles(){
            this.$store.dispatch('server/loadFiles', this.server.base_path)
            this.$message({
                type: 'success',
                message: 'Files has been reloaded'
            });
        },

        async save(){
            console.log("Saving Local Server Configuration")
            await this.$store.dispatch('app/setServer', this.server)
        },

        enterManuallyBasePath(){
            this.$prompt('Please input base path', 'Base Path for the Server', {
                confirmButtonText: 'OK',
                cancelButtonText: 'Cancel',
                // inputPattern: /[\w!#$%&'*+/=?^_`{|}~-]+(?:\.[\w!#$%&'*+/=?^_`{|}~-]+)*@(?:[\w](?:[\w-]*[\w])?\.)+[\w](?:[\w-]*[\w])?/,
                // inputErrorMessage: 'Invalid Email'
            }).then(({ value }) => {
                if(value){
                    this.server.base_path = value
                    this.$message({
                        type: 'success',
                        message: 'Your base_path has been set to: ' + value
                    });
                }
            }).catch(() => {
                this.$message({
                    type: 'info',
                    message: 'Input canceled'
                });
            });
        }

    }
}
</script>

<style lang="scss">
.input_base_path .el-form-item__content {
  width: calc(100% - 175px);
}
</style>
