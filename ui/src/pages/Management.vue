<template>
  <div class="q-pa-md">
    <div class="q-gutter-y-md" >
      <div class="row justify-center">
      <q-card style="max-width: 720px" class="col">
        <q-tabs
          v-model="tab"
          dense
          class="bg-grey-3 text-grey-7"
          active-color="primary"
          indicator-color="primary"
          align="justify"
        >
          <q-tab name="downloads" :label="$t('Downloads')" to="/management/downlods"   />
          <q-tab name="uploads" :label="$t('Uploads')"     to="/management/uploads"   />
          <q-tab name="packaging" :label="$t('Packaging')" to="/management/packaging" />
          <q-tab name="settings" :label="$t('Settings')"   to="/management/settings"  />
        </q-tabs>

        <q-tab-panels v-model="tab" animated class="bg-grey-1">
          <q-tab-panel name="downloads">
           <template v-if="Object.keys($store.state.downloads.tasks).length == 0">
             <q-icon name="info"/>
             You have an empty download list. Check out some new apps now!
           </template>
           <q-list>
           <template v-for="(task, gid) in $store.state.downloads.tasks" :key="gid">
             <!-- smart tricks, put seperator at top -->
             <!-- <q-separator spaced v-if="index !=0"/> -->
             <q-item>
               <q-item-section avatar top>
                 <q-img :src="task.details.icon_url" width="38px" />
               </q-item-section>
               <q-item-section top class="col-2 gt-sm">
                 <q-item-label class="q-mt-sm">{{$t(task.details.Name)}}</q-item-label>
               </q-item-section>
               <q-item-section top>
                 <q-linear-progress
                   stripe
                   round
                   size="25px"
                   color="green-4"
                   :value="task.aria2.completedLength / task.aria2.totalLength"
                 >
                   <div class="absolute-full flex flex-center">
                     <q-badge color="white" text-color="green-4"
                       :label="(task.aria2.completedLength / task.aria2.totalLength* 100).toFixed(2) + '%'"
                     />
                   </div>
                 </q-linear-progress>
                 <q-item-label caption lines="1">
                   Download: {{ displaySpeed( task.aria2.downloadSpeed )}}
                 </q-item-label>
                 <q-item-label caption lines="1">
                   Upload: {{ displaySpeed(task.aria2.uploadSpeed) }}
                   感觉速度不够快? 设置中开启P2P下载
                 </q-item-label>
                 <q-item-label
                   lines="1"
                   class="q-mt-xs text-body2 text-weight-bold text-primary text-uppercase"
                 >
                   <span @click="showInFolder(task)" >
                     {{$t('Open in Folder')}}
                   </span>
                 </q-item-label>
               </q-item-section>
               <q-item-section top side>
                 <div class="text-grey-8 q-gutter-xs">
                   <q-btn class="gt-xs" size="12px"
                     dense color="primary"
                     :label="$t('Install')"
                   />
                   <q-btn
                     class="gt-xs" size="12px"
                     dense color="red-5" :label="$t('Delete')"
                     @click="$store.dispatch('downloads/downloadTaskRemove',task.aria2.gid)"
                   />
                   <q-btn size="12px" flat dense round icon="more_vert" />
                 </div>
               </q-item-section>
             </q-item>
           </template>
           </q-list>
          </q-tab-panel>

          <q-tab-panel name="uploads">
           <q-list class="rounded">
           <template v-for="(item, index) in uploadList" :key="item.name">
             <!-- smart tricks, put seperator at top -->
             <q-separator spaced v-if="index !=0"/>
             <q-item>
               <q-item-section avatar top>
                 <q-icon name="account_tree" color="black" size="34px" />
               </q-item-section>
               <q-item-section top class="col-2 gt-sm">
                 <q-item-label class="q-mt-sm">应用名字</q-item-label>
               </q-item-section>
               <q-item-section top>
                 <q-linear-progress
                   stripe
                   round
                   size="25px"
                   color="purple-4"
                   :value="progress"
                 >
                   <div class="absolute-full flex flex-center">
                     <q-badge
                       color="white"
                       text-color="purple-4"
                       :label="progressLabel"
                    />
                   </div>
                 </q-linear-progress>
                 <q-item-label caption lines="1">
                   感觉速度不够快? 设置中开启P2P上传
                 </q-item-label>
                 <q-item-label lines="1" class="q-mt-xs text-body2 text-weight-bold
                   text-primary text-uppercase">
                   <span class="cursor-pointer">
                     {{$t('Open in Folder')}}
                   </span>
                 </q-item-label>
               </q-item-section>
               <q-item-section top side>
                 <div class="text-grey-8 q-gutter-xs">
                   <q-btn class="gt-xs" size="12px" dense color="primary" :label="$t('Install')" />
                   <q-btn class="gt-xs" size="12px" dense color="red-5" :label="$t('Delete')" />
                   <q-btn size="12px" flat dense round icon="more_vert" />
                 </div>
               </q-item-section>
             </q-item>
           </template>
         </q-list>

          </q-tab-panel>

          <q-tab-panel name="packaging">
            <q-stepper
              v-model="step"
              vertical
              flat
              class="bg-grey-1"
              color="primary"
              animated
            >
              <q-step
                :name="1"
                :title="$t('Introduction to application packaging')"
                icon="settings"
                :done="step > 1"
              >
                {{$t('此助手能帮助用户一步一步完成应用上传')}}

                <q-stepper-navigation>
                  <q-btn
                    @click="step = 2"
                    color="primary"
                    :label="$t('Continue')"
                  />
                </q-stepper-navigation>
              </q-step>

              <q-step
                :name="2"
                title="Create an ad group"
                caption="Optional"
                icon="create_new_folder"
                :done="step > 2"
              >
                An ad group contains one or more ads which target a shared set of keywords.

                <q-stepper-navigation>
                  <q-btn @click="step = 4"
                    color="primary"
                    :label="$t('Continue')"
                  />
                  <q-btn flat @click="step = 1" color="primary"
                    :label="$t('Back')"
                    class="q-ml-sm"
                  />
                </q-stepper-navigation>
              </q-step>

              <q-step
                :name="3"
                title="Ad template"
                icon="assignment"
                disable
              >
                This step won't show up because it is disabled.
              </q-step>

              <q-step
                :name="4"
                title="Create an ad"
                icon="add_comment"
              >
                Try out different ad text to see what brings in the most customers, and learn how to
                enhance your ads using features like ad extensions. If you run into any problems with
                your ads, find out how to tell if they're running and how to resolve approval issues.

                <q-stepper-navigation>
                  <q-btn color="primary" :label="$t('Upload')" />
                  <q-btn flat
                    @click="step = 2"
                    color="primary"
                    class="q-ml-sm"
                    :label="$t('Back')"
                  />
                </q-stepper-navigation>
              </q-step>
            </q-stepper>
          </q-tab-panel>

          <q-tab-panel name="settings">
            <q-list>
              <q-item-label header>
                {{$t('App Control')}}
              </q-item-label>

              <q-item clickable v-ripple>
                <q-item-section>
                  <q-item-label>
                    {{$t('Sandbox Management')}}
                  </q-item-label>
                  <q-item-label caption>
                    Set the content filtering level to restrict
                    apps that can be downloaded
                  </q-item-label>
                </q-item-section>
              </q-item>

              <q-item clickable v-ripple>
                <q-item-section>
                  <q-item-label>Password</q-item-label>
                  <q-item-label caption>
                    Require password for purchase or use
                    password to restrict purchase
                  </q-item-label>
                </q-item-section>
              </q-item>

          <q-separator spaced />

          <q-item-label header>{{$t('General Setting')}}</q-item-label>
          <template
            v-for="option,index in $store.state.settings.generalSetting"
            :key="option.title"
          >
          <q-item tag="label" v-ripple>
            <q-item-section side top>
              <!-- since we are manipulateing element in an array, we have to do v-model update ourselves -->
              <q-checkbox
                :model-value="option.enabled"
                @update:model-value="v => $store.commit('settings/toggleGeneralSetting', index)"
              />
            </q-item-section>
            <q-item-section>
              <q-item-label>{{$t(option.title)}}</q-item-label>
              <q-item-label caption>
                {{$t(option.subtitle)}}
              </q-item-label>
            </q-item-section>
          </q-item>
          </template>

          <q-separator spaced />

          <q-item-label header>{{$t("Privacy Setting")}}</q-item-label>
          <template
            v-for="option,index in $store.state.settings.privacySetting"
            :key="option.title"
          >
          <q-item tag="label" v-ripple>
            <q-item-section>
              <q-item-label>{{$t(option.title)}}</q-item-label>
              <q-item-label caption>{{$t(option.subtitle)}}</q-item-label>
            </q-item-section>
            <q-item-section side >
              <q-toggle color="blue" val="battery"
                :model-value="option.enabled"
                @update:model-value="v => $store.commit('settings/togglePrivacySetting', index)"
              />
            </q-item-section>
          </q-item>
          </template>

          <q-separator spaced />

          <q-item-label header>Other settings</q-item-label>
          <q-item>
            <q-item-section side>
              <q-icon color="teal" name="volume_down" />
            </q-item-section>
            <q-item-section>
              <q-slider
                v-model="volume"
                :min="0"
                :max="10"
                label
                color="teal"
              />
            </q-item-section>
            <q-item-section side>
              <q-icon color="teal" name="volume_up" />
            </q-item-section>
          </q-item>

          <q-item>
            <q-item-section side>
              <q-icon color="deep-orange" name="brightness_medium" />
            </q-item-section>
            <q-item-section>
              <q-slider
                v-model="brightness"
                :min="0"
                :max="10"
                label
                color="deep-orange"
              />
            </q-item-section>
          </q-item>

          <q-item>
            <q-item-section side>
              <q-icon color="primary" name="mic" />
            </q-item-section>
            <q-item-section>
              <q-slider
                v-model="mic"
                :min="0"
                :max="50"
                label
              />
            </q-item-section>
          </q-item>
          </q-list>
          </q-tab-panel>
        </q-tab-panels>
      </q-card>
      </div>
    </div>
  </div>
</template>


<script>
import { defineComponent, ref, computed } from 'vue';
import { useStore } from 'vuex';

const uploadList = [
  {
    name : "AppName1",
  },
  {
    name : "AppName2",
  },
];

export default defineComponent({
    name: 'Management',
    setup() {
      const progress = ref(0.7);
      const $store = useStore();
      return {
        notif1: ref(true),
        notif2: ref(true),
        notif3: ref(false),
        volume: ref(6),
        brightness: ref(3),
        mic: ref(8),
        step: ref(1),
        progress,
        progressLabel: computed(() => (progress.value * 100).toFixed(2) + '%'),
        uploadList,
     }
    },
    data() {
      return {
        tab: this.$route.params.tab,
      }
    },
    methods: {
      displaySpeed: function(speed) {
        if (speed < 1024) {
          return speed + ' B/s';
        } else if (speed < 1024*1024) {
          return (speed/1024).toFixed(2) + ' KB/s';
        } else if (speed < 1024*1024*1024) {
          return (speed/1024/1024/1024).toFixed(2) + ' MB/s';
        } else {
          return (speed/1024/1024/1024/1024).toFixed(2) + ' GB/s';
        }
      },
      showInFolder: function(task) {
        let folder = task.aria2.dir;
        // const {shell} = require('electron') // deconstructing assignment
        // shell.showItemInFolder(folder) // Show the given file in a file manager. If possible, select the file.
        // shell.openPath(folder) // Open the given file in the desktop's default manner.
        console.log('Open in folder', task.aria2.dir);
      }
    },
    mounted() {
    }
})
</script>
