<template>
<router-link class="ldlomzub" :to="url" v-user-preview="canonical" v-if="url.startsWith('/')">
	<span class="me" v-if="isMe">{{ $t('you') }}</span>
	<span class="main">
		<span class="username">@{{ username }}</span>
		<span class="host" v-if="(host != localHost) || $store.state.settings.showFullAcct">@{{ toUnicode(host) }}</span>
	</span>
</router-link>
<a class="ldlomzub" :href="url" target="_blank" rel="noopener" v-else>
	<span class="main">
		<span class="username">@{{ username }}</span>
		<span class="host">@{{ toUnicode(host) }}</span>
	</span>
</a>
</template>

<script lang="ts">
import Vue from 'vue';
import { toUnicode } from 'punycode';
import { host as localHost } from '../config';

export default Vue.extend({
	props: {
		username: {
			type: String,
			required: true
		},
		host: {
			type: String,
			required: true
		}
	},
	data() {
		return {
			localHost
		};
	},
	computed: {
		url(): string {
			switch (this.host) {
				case 'twitter.com':
				case 'github.com':
					return `https://${this.host}/${this.username}`;
				default:
					return `/${this.canonical}`;
			}
		},
		canonical(): string {
			return this.host === localHost ? `@${this.username}` : `@${this.username}@${toUnicode(this.host)}`;
		},
		isMe(): boolean {
			return this.$store.getters.isSignedIn && (
				`@${this.username}@${toUnicode(this.host)}` === `@${this.$store.state.i.username}@${toUnicode(localHost)}`.toLowerCase()
			);
		}
	},
	methods: {
		toUnicode
	}
});
</script>

<style lang="scss" scoped>
.ldlomzub {
	color: var(--mention);
	
	> .me {
		pointer-events: none;
		user-select: none;
		font-size: 70%;
		vertical-align: top;
	}

	> .main {
		> .host {
			opacity: 0.5;
		}
	}
}
</style>
