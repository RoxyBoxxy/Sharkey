<!--
SPDX-FileCopyrightText: syuilo and other misskey contributors
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
<div v-if="hide" :class="[$style.hidden, (video.isSensitive && defaultStore.state.highlightSensitiveMedia) && $style.sensitiveContainer]" @click="hide = false">
	<!-- 【注意】dataSaverMode が有効になっている際には、hide が false になるまでサムネイルや動画を読み込まないようにすること -->
	<div :class="$style.sensitive">
		<b v-if="video.isSensitive" style="display: block;"><i class="ti ti-alert-triangle"></i> {{ i18n.ts.sensitive }}{{ defaultStore.state.enableDataSaverMode ? ` (${i18n.ts.video}${video.size ? ' ' + bytes(video.size) : ''})` : '' }}</b>
		<b v-else style="display: block;"><i class="ti ti-movie"></i> {{ defaultStore.state.enableDataSaverMode && video.size ? bytes(video.size) : i18n.ts.video }}</b>
		<span>{{ i18n.ts.clickToShow }}</span>
	</div>
</div>
<div v-else :class="[$style.visible, (video.isSensitive && defaultStore.state.highlightSensitiveMedia) && $style.sensitiveContainer]">
	<video
		:class="$style.video"
		:poster="video.thumbnailUrl"
		:title="video.comment"
		:alt="video.comment"
		preload="none"
		controls
		@contextmenu.stop
	>
		<source
			:src="video.url"
		>
	</video>
	<i class="ti ti-eye-off" :class="$style.hide" @click="hide = true"></i>
</div>
</template>

<script lang="ts" setup>
import { ref } from 'vue';
import * as Misskey from 'misskey-js';
import bytes from '@/filters/bytes.js';
import { defaultStore } from '@/store.js';
import { i18n } from '@/i18n.js';

const props = defineProps<{
	video: Misskey.entities.DriveFile;
}>();

const hide = ref((defaultStore.state.nsfw === 'force' || defaultStore.state.enableDataSaverMode) ? true : (props.video.isSensitive && defaultStore.state.nsfw !== 'ignore'));
</script>

<style lang="scss" module>
.visible {
	position: relative;
}

.sensitiveContainer {
	position: relative;

	&::after {
		content: "";
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		pointer-events: none;
		border-radius: inherit;
		box-shadow: inset 0 0 0 4px var(--warn);
	}
}

.hide {
	display: block;
	position: absolute;
	border-radius: 6px;
	background-color: black;
	color: var(--accentLighten);
	font-size: 14px;
	opacity: .5;
	padding: 3px 6px;
	text-align: center;
	cursor: pointer;
	top: 12px;
	right: 12px;
}

.video {
	display: flex;
	justify-content: center;
	align-items: center;
	font-size: 3.5em;
	overflow: hidden;
	background-position: center;
	background-size: cover;
	width: 100%;
	height: 100%;
}

.hidden {
	display: flex;
	justify-content: center;
	align-items: center;
	background: #111;
	color: #fff;
}

.sensitive {
	display: table-cell;
	text-align: center;
	font-size: 12px;
}
.indicators {
	display: inline-flex;
	position: absolute;
	top: 10px;
	left: 10px;
	pointer-events: none;
	opacity: .5;
	gap: 6px;
}

.indicator {
	/* Hardcode to black because either --bg or --fg makes it hard to read in dark/light mode */
	background-color: black;
	border-radius: 6px;
	color: var(--accentLighten);
	display: inline-block;
	font-weight: bold;
	font-size: 0.8em;
	padding: 2px 5px;
}
</style>
