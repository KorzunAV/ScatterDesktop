<template>
    <section>

        <section class="fader" :class="{'show':showFader}">
            <figure class="bg" @click="clickedFader"></figure>

            <section v-if="nextPopIn">
                <section class="pop-in">
                    <prompt v-if="nextPopIn.data.type === popupTypes.PROMPT"></prompt>
                    <text-prompt v-if="nextPopIn.data.type === popupTypes.TEXT_PROMPT"></text-prompt>
                    <selector v-if="nextPopIn.data.type === popupTypes.SELECTOR"></selector>
                    <mnemonic v-if="nextPopIn.data.type === popupTypes.MNEMONIC"></mnemonic>
                </section>
            </section>

        </section>


        <section class="snackbar-holder" :class="{'has-snackbar':snackbars.length}">
            <transition-group name="snackbar-transition">
                <snackbar :popup="popup" v-for="popup in snackbars" :key="popup.id"></snackbar>
            </transition-group>
        </section>

        <!--<section class="snackbar-holder">-->
            <!--<snackbar :popup="{data:{props:{message:'This is just some message whatever', icon:'trash-o'}}}"></snackbar>-->
        <!--</section>-->



    </section>
</template>

<script>
    import {RouteNames, RouteDepth} from '../vue/Routing'
    import { mapActions, mapGetters, mapState } from 'vuex'
    import * as Actions from '../store/constants';
    import {PopupDisplayTypes, PopupTypes} from '../models/popups/Popup'

    export default {
        data(){ return {
            popupTypes:PopupTypes,
            popupDisplayTypes:PopupDisplayTypes,
        }},
        computed:{
            ...mapState([
                'popups'
            ]),
            ...mapGetters([
                'nextPopIn',
                'snackbars',
            ]),
            showFader(){
                return this.nextPopIn && this.nextPopIn.displayType === PopupDisplayTypes.POP_IN
            }
        },
        methods:{
            clickedFader(){
                if(this.nextPopIn && this.nextPopIn.displayType === PopupDisplayTypes.POP_IN)
                    this[Actions.RELEASE_POPUP](this.nextPopIn);
            },
            ...mapActions([
                Actions.RELEASE_POPUP
            ])
        }
    }
</script>

<style scoped lang="scss" rel="stylesheet/scss">
    @import "../_variables.scss";

    .snackbar-holder {
        position:fixed;
        bottom:0;
        left:0;
        right:0;
        text-align:center;
        z-index:10001;

        &.has-snackbar {
            padding-bottom:20px;
        }
    }

    .pop-in {
        -webkit-app-region: no-drag;
        background:#fff;
        border-radius:2px;
        box-shadow:0 10px 50px rgba(0,0,0,0.1), 0 0 250px rgba(0,0,0,0.1);
    }

    .fader {

        display: flex;
        justify-content: center;
        align-items: center;

        position:fixed;
        top:0;
        bottom:0;
        left:0;
        right:0;
        //background:$light-grey;
        opacity:0;
        visibility: hidden;
        transition: all 0.5s ease;
        transition-property: opacity, visibility;

        z-index:10000;

        &.show {
            opacity:1;
            visibility: visible;
        }

        .bg {
            cursor: pointer;
            position:fixed;
            top:0;
            bottom:0;
            left:0;
            right:0;
            background:rgba(255,255,255,0.8);
            z-index: -1;
        }
    }


    $speed:0.3s;
    .snackbar-transition-leave-active,
    .snackbar-transition-enter-active {
        /*position:absolute;*/
        /*bottom:0; left:0; right:0;*/
        transition: $speed;
    }

    .snackbar-transition-enter {
        opacity:0;
        transform: translate(0, 100%);
    }

    .snackbar-transition-leave-to {
        opacity:0;
        transform: translate(0, 20%);
    }
</style>
