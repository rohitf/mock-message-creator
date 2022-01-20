<script lang="ts">
	import { browser } from "$app/env";
	import { fly } from "svelte/transition";

	interface Message {
		ownMessage: boolean;
		content: string;
	}
	let messages: Message[] = [];

	if (browser) {
		const savedMessages = JSON.parse(localStorage.getItem("messages"));
		messages = savedMessages || [
			{
				ownMessage: true,
				content: "Hey, any tips for the exam?",
			},
			{
				ownMessage: false,
				content: "Do not flee in my car",
			},
			{
				ownMessage: true,
				content: "Umm...",
			},
		];
	}

	$: if (browser && messages.length) {
		localStorage.setItem("messages", JSON.stringify(messages));
	}

	let chattingWith = "The General";
	$: initials = chattingWith
		.split(" ")
		.map((s) => s.charAt(0).toUpperCase())
		.join("");

	function addMessage() {
		debugger;
		messages = [
			...messages,
			{
				ownMessage: true,
				content: "🙂",
			},
		];
	}

	function deleteMessage(event: MouseEvent, index) {
		event.preventDefault();
		messages = [...messages.slice(0, index), ...messages.slice(index + 1)];
	}
</script>

<body class="p-4">
	<div class="grid place-items-center text-center">
		<main class="mt-4 rounded-md shadow-md w-5/6 sm:w-96 ">
			<section class="header flex justify-center p-4 rounded-t-md">
				<i class="fas fa-comment-alt text-2xl" />
				<div
					contenteditable
					spellcheck="false"
					class="text-xl bg-transparent ml-3 whitespace-pre"
					bind:textContent={chattingWith}
				>
					{chattingWith}
				</div>
			</section>
			<section
				class="screen flex flex-col h-[500px] bg-white pb-4 rounded-b-md overflow-y-scroll
				px-4"
				on:dblclick={addMessage}
			>
				{#each messages as { ownMessage, content }, i}
					{#if ownMessage}
						<div
							transition:fly={{ x: 200, duration: 400 }}
							class="flex mt-4 justify-end"
						>
							<div
								contenteditable
								spellcheck="false"
								on:contextmenu={(e) => deleteMessage(e, i)}
								on:input={(e) =>
									(messages[i].content = e.target.innerText)}
								class="mr-2"
							>
								{content}
							</div>
							<div
								class="avatar w-10 bg-blue-500"
								on:click={() => (ownMessage = !ownMessage)}
							>
								<i class="fas fa-user" />
							</div>
						</div>
					{:else}
						<div
							transition:fly={{ x: -200, duration: 400 }}
							class="flex mt-4"
						>
							<div
								class="avatar w-12 bg-purple-500"
								on:click={() => (ownMessage = !ownMessage)}
							>
								{initials}
							</div>
							<div
								contenteditable
								spellcheck="false"
								on:contextmenu={(e) => deleteMessage(e, i)}
								on:input={(e) => {
									console.log(e.target);
									messages[i].content = e.target.innerText;
								}}
								class="ml-2"
							>
								{content}
							</div>
						</div>
					{/if}
				{/each}
			</section>
		</main>
		<div class="mt-8 text-2xl text-gray-700">
			<ul class="list-disc text-left">
				<li>Click name in header to edit</li>
				<li>Double click screen to add message</li>
				<li>Click message to edit</li>
				<li>Click sender icon to switch sender</li>
				<li>Right click message to remove</li>
				<li>Scroll to see all messages</li>
			</ul>
		</div>
	</div>
</body>

<style>
	body {
		background: linear-gradient(
			270deg,
			rgba(34, 193, 195, 1) 0%,
			rgb(251, 203, 100) 100%
		);
	}

	.header {
		background: rgba(255, 255, 255, 0.6);
		backdrop-filter: blur(10px);
	}

	.avatar {
		@apply grid place-items-center text-white h-10 rounded-full cursor-pointer;
	}

	.screen [contenteditable] {
		@apply bg-gray-200 rounded-lg max-w-[12.5rem] mr-2 p-3 text-left whitespace-normal;
	}
</style>
