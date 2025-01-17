<template>
    <Portal v-if="fullScreen">
        <div v-if="containerVisible" :ref="maskRef" :class="cx('mask')" :role="fullScreen ? 'dialog' : 'region'" :aria-modal="fullScreen ? 'true' : undefined" v-bind="ptm('mask')">
            <transition name="p-galleria" @before-enter="onBeforeEnter" @enter="onEnter" @before-leave="onBeforeLeave" @after-leave="onAfterLeave" appear>
                <GalleriaContent v-if="visible" :ref="containerRef" v-focustrap @mask-hide="maskHide" :templates="$slots" @activeitem-change="onActiveItemChange" :pt="pt" :unstyled="unstyled" v-bind="$props" />
            </transition>
        </div>
    </Portal>
    <GalleriaContent v-else :templates="$slots" @activeitem-change="onActiveItemChange" :pt="pt" :unstyled="unstyled" v-bind="$props" />
</template>

<script>
import BaseGalleria from './BaseGalleria.vue';
import FocusTrap from 'primevue/focustrap';
import Portal from 'primevue/portal';
import { DomHandler, ZIndexUtils } from 'primevue/utils';
import GalleriaContent from './GalleriaContent.vue';

export default {
    name: 'Galleria',
    extends: BaseGalleria,
    inheritAttrs: false,
    emits: ['update:activeIndex', 'update:visible'],
    props: {
        id: {
            type: String,
            default: null
        },
        value: {
            type: Array,
            default: null
        },
        activeIndex: {
            type: Number,
            default: 0
        },
        fullScreen: {
            type: Boolean,
            default: false
        },
        visible: {
            type: Boolean,
            default: false
        },
        numVisible: {
            type: Number,
            default: 3
        },
        responsiveOptions: {
            type: Array,
            default: null
        },
        showItemNavigators: {
            type: Boolean,
            default: false
        },
        showThumbnailNavigators: {
            type: Boolean,
            default: true
        },
        showItemNavigatorsOnHover: {
            type: Boolean,
            default: false
        },
        changeItemOnIndicatorHover: {
            type: Boolean,
            default: false
        },
        circular: {
            type: Boolean,
            default: false
        },
        autoPlay: {
            type: Boolean,
            default: false
        },
        transitionInterval: {
            type: Number,
            default: 4000
        },
        showThumbnails: {
            type: Boolean,
            default: true
        },
        thumbnailsPosition: {
            type: String,
            default: 'bottom'
        },
        verticalThumbnailViewPortHeight: {
            type: String,
            default: '300px'
        },
        showIndicators: {
            type: Boolean,
            default: false
        },
        showIndicatorsOnItem: {
            type: Boolean,
            default: false
        },
        indicatorsPosition: {
            type: String,
            default: 'bottom'
        },
        baseZIndex: {
            type: Number,
            default: 0
        },
        maskClass: {
            type: String,
            default: null
        },
        containerStyle: {
            type: null,
            default: null
        },
        containerClass: {
            type: null,
            default: null
        },
        containerProps: {
            type: null,
            default: null
        },
        prevButtonProps: {
            type: null,
            default: null
        },
        nextButtonProps: {
            type: null,
            default: null
        }
    },
    container: null,
    mask: null,
    data() {
        return {
            containerVisible: this.visible
        };
    },
    updated() {
        if (this.fullScreen && this.visible) {
            this.containerVisible = this.visible;
        }
    },
    beforeUnmount() {
        if (this.fullScreen) {
            !this.isUnstyled && DomHandler.removeClass(document.body, 'p-overflow-hidden');
        }

        this.mask = null;

        if (this.container) {
            ZIndexUtils.clear(this.container);
            this.container = null;
        }
    },
    methods: {
        onBeforeEnter(el) {
            ZIndexUtils.set('modal', el, this.baseZIndex || this.$primevue.config.zIndex.modal);
        },
        onEnter(el) {
            this.mask.style.zIndex = String(parseInt(el.style.zIndex, 10) - 1);
            !this.isUnstyled && DomHandler.addClass(document.body, 'p-overflow-hidden');
            this.focus();
        },
        onBeforeLeave() {
            !this.isUnstyled && DomHandler.addClass(this.mask, 'p-component-overlay-leave');
        },
        onAfterLeave(el) {
            ZIndexUtils.clear(el);
            this.containerVisible = false;
            !this.isUnstyled && DomHandler.removeClass(document.body, 'p-overflow-hidden');
        },
        onActiveItemChange(index) {
            if (this.activeIndex !== index) {
                this.$emit('update:activeIndex', index);
            }
        },
        maskHide() {
            this.$emit('update:visible', false);
        },
        containerRef(el) {
            this.container = el;
        },
        maskRef(el) {
            this.mask = el;
        },
        focus() {
            let focusTarget = this.container.$el.querySelector('[autofocus]');

            if (focusTarget) {
                focusTarget.focus();
            }
        }
    },
    components: {
        GalleriaContent: GalleriaContent,
        Portal: Portal
    },
    directives: {
        focustrap: FocusTrap
    }
};
</script>
