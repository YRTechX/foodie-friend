<template>
      <v-navigation-drawer
        expand-on-hover
        rail
        class="d-none d-lg-flex"
      >
        <v-list>
            <v-list-item
            :title="userName"
            :subtitle="userEmail"
            to="/user-profile"
            >
              <template v-slot:prepend>
                <v-avatar>
                    <v-img
                    :src="userPhoto"
                    :lazy-src="userPhoto"
                    aspect-ratio="1/1"
                    >
                        <template v-slot:placeholder>
                            <div class="d-flex align-center justify-center fill-height" >
                                <v-progress-circular
                                color="grey-lighten-4"
                                indeterminate
                                :size="20"
                                ></v-progress-circular>
                            </div>
                        </template>
                    </v-img>
                </v-avatar>
              </template>
            </v-list-item>
        </v-list>

        <v-divider></v-divider>

        <v-list density="compact" nav>
            <v-list-item prepend-icon="mdi-map-search-outline" title="Поиск заведения" value="search" to="/application"></v-list-item>
        </v-list>
        <v-divider></v-divider>
        <template v-slot:append>
        <v-divider></v-divider>
        <v-list density="compact" nav>
            <v-list-item prepend-icon="mdi-logout" title="Выйти" value="Выйти" @click="$store.dispatch('signOut')"></v-list-item>
        </v-list>
        </template>
      </v-navigation-drawer>

        <v-app-bar
        color="indigo-lighten-1"
        density="compact"
      >
      <v-app-bar-title>Foodie Friend</v-app-bar-title>
        <template v-slot:prepend>
          <v-app-bar-nav-icon @click="isMenu = !isMenu" class="d-lg-none"></v-app-bar-nav-icon>
        </template>
  
          <v-spacer></v-spacer>
          <template v-slot:append v-if="showButton">
            <v-btn @click="TOGGLE_FILTERS" v-if="userLocation">
              <v-icon>mdi-filter-outline</v-icon>
              <template class="d-none d-sm-flex">ФИЛЬТРЫ</template>
            </v-btn>

            <v-btn @click="handleClick" v-if="!editUserLocationListener">
              <v-icon>mdi-map-marker-radius-outline</v-icon>
              <template class="d-none d-sm-flex">ИЗМЕНИТЬ ЛОКАЦИЮ</template>
              <v-overlay
                v-model="isAlertOn"
                location-strategy="connected"
                scroll-strategy="block"
                class="align-center justify-center"
              >
                <v-alert type="success" text="Режим изменения локации включен"></v-alert>
              </v-overlay>
            </v-btn>

            <v-btn @click="removeEditUserLocationListener" v-if="editUserLocationListener">
              <v-icon>mdi-map-marker-remove-outline</v-icon>
              <template class="d-none d-sm-flex">ОТМЕНА</template>
            </v-btn>

          </template>
        </v-app-bar>


      <template class="d-block d-lg-none">
        
      <v-navigation-drawer
        v-model="isMenu"
        temporary
      >
        <v-list>
            <v-list-item
            :title="userName"
            :subtitle="userEmail"
            to="/user-profile"
            >
              <template v-slot:prepend>
                <v-avatar>
                    <v-img
                    :src="userPhoto"
                    :lazy-src="userPhoto"
                    aspect-ratio="1/1"
                    >
                        <template v-slot:placeholder>
                            <div class="d-flex align-center justify-center fill-height" >
                                <v-progress-circular
                                color="grey-lighten-4"
                                indeterminate
                                :size="20"
                                ></v-progress-circular>
                            </div>
                        </template>
                    </v-img>
                </v-avatar>
              </template>
            </v-list-item>
        </v-list>

        <v-divider></v-divider>

        <v-list density="compact" nav>
            <v-list-item prepend-icon="mdi-map-search-outline" title="Поиск заведения" value="search" to="/application"></v-list-item>
        </v-list>
        <v-divider></v-divider>
        <template v-slot:append>
        <v-divider></v-divider>
        <v-list density="compact" nav>
            <v-list-item prepend-icon="mdi-logout" title="Выйти" value="Выйти" @click="$store.dispatch('signOut')"></v-list-item>
        </v-list>
        </template>
      </v-navigation-drawer>
      </template>
</template>

<script>
import { mapActions, mapMutations, mapState } from "vuex"
export default {
    name: 'my-header',
    props: {
      showButton: {
        type: Boolean,
        default: true, // По умолчанию кнопка показывается, если значение не передано
      },
    },
    data(){
        return {
            isMenu: null,
            isAlertOn: false,
        }
    },
    watch: {
      isAlertOn(){
        this.isAlertOn ? setTimeout(() => {this.isAlertOn ? this.isAlertOn = !this.isAlertOn : ''}, 1500) : ''
      }
    },
    computed: {
        ...mapState({
            editUserLocationListener: state => state.googleMaps.editUserLocationListener,
            userLocation: state => state.googleMaps.userLocation,
        }),
        userInfo() {
            return this.$store.state.auth.user.userData || {}
        },
        userName(){
            const userInfo = Array.isArray(this.userInfo) ? this.userInfo : [this.userInfo]
            const field = userInfo.find((field) => field.hasOwnProperty('Имя'))
            return field ? field['Имя'] : ''
        },
        userPhoto(){
            return this.$store.state.auth.user.photo_URL
        },
        userEmail(){
            const userInfo = Array.isArray(this.userInfo) ? this.userInfo : [this.userInfo]
            const field = userInfo.find((field) => field.hasOwnProperty('E-mail'))
            return field ? field['E-mail'] : ''
        },
    },
    methods: {
        ...mapMutations(['TOGGLE_FILTERS']),
        ...mapActions(['newUserLocation', 'removeEditUserLocationListener']),
        async handleClick(){
          this.isAlertOn = !this.isAlertOn
          await this.$nextTick()
          setTimeout(() => {
            this.newUserLocation();
          }, 1000);
        }
    },
}
</script>
