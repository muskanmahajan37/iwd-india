<template>
  <v-container class="px-3">
    <v-layout wrap align-center justify-center row fill-heights>
      <v-flex xs12 sm12 md12 lg12 xl12 class="pa-5">
        <v-layout>
          <v-flex xs12 md5 class="px-1">
            <v-select
              v-model="SelectedItem"
              :items="items"
              chips
              label="Filter by Track"
              small-chips
              multiple
              outlined
              v-on:change="FilterData()"
            >
              <template v-slot:prepend-item>
                <v-list-item ripple @click="toggle">
                  <v-list-item-action>
                    <v-icon :color="SelectedItem.length > 0 ? 'indigo darken-4' : ''">{{ icon }}</v-icon>
                  </v-list-item-action>
                  <v-list-item-content>
                    <v-list-item-title>Select All</v-list-item-title>
                  </v-list-item-content>
                </v-list-item>
                <v-divider class="mt-2"></v-divider>
              </template>
              <template v-slot:append-item>
                <v-divider class="mb-2"></v-divider>
                <v-list-item disabled>
                  <v-list-item-avatar color="grey lighten-3">
                    <v-icon>mdi-animation</v-icon>
                  </v-list-item-avatar>

                  <v-list-item-content v-if="selectAllTag">
                    <v-list-item-title>Holy smokes, someone call the Web Developer!</v-list-item-title>
                  </v-list-item-content>

                  <v-list-item-content v-else-if="selectSomeTag">
                    <v-list-item-title>Tags Count</v-list-item-title>
                    <v-list-item-subtitle>{{ SelectedItem.length }}</v-list-item-subtitle>
                  </v-list-item-content>

                  <v-list-item-content v-else>
                    <v-list-item-title>How could you not like Tags?</v-list-item-title>
                    <v-list-item-subtitle>Go ahead, make a selection above!</v-list-item-subtitle>
                  </v-list-item-content>
                </v-list-item>
              </template>
            </v-select>
          </v-flex>
        </v-layout>
        <v-layout row wrap class="px-3">
          <v-flex xs12 sm4 md4 lg3 v-for="(item,index) in FilterData()" :key="index">
            <div
              style="border-radius: 5px; border:1px solid #e0e0e0;min-height:180px"
              class="ma-1 pa-5"
            >
              <sessionDialog :data="{vdata:item}" />
            </div>
          </v-flex>
        </v-layout>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
import sessionsData from "@/assets/data/sessions.json";
export default {
  components: {
    sessionDialog: () => import("@/components/common/sessionDialog")
  },
  data: () => ({
    sessionsData: sessionsData,
    items: [],
    SelectedItem: []
  }),
  computed: {
    selectAllTag() {
      return this.SelectedItem.length === this.items.length;
    },
    selectSomeTag() {
      return this.SelectedItem.length > 0 && !this.selectAllTag;
    },
    icon() {
      if (this.selectAllTag) return "mdi-close-box";
      if (this.selectSomeTag) return "mdi-minus-box";
      return "mdi-checkbox-blank-outline";
    }
  },
  mounted() {
    this.items = [...new Set(this.sessionsData.map(res => res.track))];
  },
  methods: {
    toggle() {
      this.$nextTick(() => {
        if (this.selectAllTag) {
          this.SelectedItem = [];
        } else {
          this.SelectedItem = this.items.slice();
        }
      });
    },
    ShuffleData(sessionsData) {
      let currentIndex = sessionsData.length,
        temporaryValue,
        randomIndex;
      while (0 !== currentIndex) {
        randomIndex = Math.floor(Math.random() * currentIndex);
        currentIndex -= 1;
        temporaryValue = sessionsData[currentIndex];
        sessionsData[currentIndex] = sessionsData[randomIndex];
        sessionsData[randomIndex] = temporaryValue;
      }
      return sessionsData;
    },
    FilterData() {
      if (this.SelectedItem.length > 0) {
        // this.SelectedItem = [...new Set(names)];
        let asData = [];
        this.SelectedItem.map(val => {
          this.sessionsData.filter(res => {
            if (res.track == val) {
              asData.push(res);
            }
          });
        });
        return this.ShuffleData(asData);
      } else {
        return this.ShuffleData(this.sessionsData);
      }
    }
  }
};
</script>
