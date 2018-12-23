<template>
	<div class="discord-message">
		<div class="author-avatar">
			<img :src="avatarSrc" :alt="author" />
		</div>
		<div class="message-content">
			<div v-if="!compactMode">
				<author-info :bot="bot" :role-color="roleColor">
					{{ author }}
				</author-info>
			</div>
			<div :class="{ 'highlight-mention': highlightMention }" class="message-body">
				<author-info v-if="compactMode" :bot="bot" :role-color="roleColor">
					{{ author }}
				</author-info>
				<slot></slot>
			</div>
			<slot name="embeds"></slot>
		</div>
	</div>
</template>

<script>
import AuthorInfo from './AuthorInfo.vue';

export default {
	name: 'DiscordMessage',

	components: { AuthorInfo },

	props: {
		author: {
			type: String,
			default: 'User',
		},
		avatar: String,
		bot: Boolean,
		roleColor: String,
	},

	data() {
		return {
			avatarSrc: '',
			highlightMention: false,
			compactMode: false,
		};
	},

	created() {
		const defaultAvatars = this.$root.$discordAvatars;

		this.avatarSrc = defaultAvatars[this.avatar] || this.avatar || defaultAvatars.default;
		this.compactMode = this.$parent.$props.compactMode;
	},

	mounted() {
		this.highlightMention = this.$children.some(child => {
			return child.$options.name === 'Mention' && child.$props.highlight && child.$props.type !== 'channel';
		});
	},
};
</script>

<style>
.discord-message {
	display: flex;
	font-size: 0.9em;
	padding: 1em 0.5em;
	border-bottom: 1px solid rgba(255, 255, 255, 0.05);
}

.discord-light-theme .discord-message {
	border-color: #eceeef;
}

.discord-message:last-of-type {
	border-bottom-width: 0;
}

.discord-message a {
	color: #0096cf;
	font-weight: normal;
	text-decoration: none;
}

.discord-light-theme .discord-message a {
	color: #00b0f4;
}

.discord-message a:hover {
	text-decoration: underline;
}

.discord-message .author-avatar {
	margin-right: 1rem;
	min-width: 40px;
}

.discord-message .author-avatar img {
	width: 40px;
	height: 40px;
	border-radius: 50%;
}

.discord-message .message-content {
	width: 100%;
}

.discord-message .message-body {
	position: relative;
}

.compact-mode .discord-message {
	padding-top: 0.5em;
	padding-bottom: 0.5em;
}

.compact-mode .author-avatar {
	display: none;
}

.compact-mode .message-body {
	margin-left: 0.25em;
}
</style>