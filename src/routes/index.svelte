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

<body class="w-screen h-screen p-4">
	<div class="grid place-items-center text-center">
		<main class="mt-4 w-fit rounded-md shadow-md">
			<section class="header flex justify-center w-96 p-4 rounded-t-md">
				<i class="fas fa-comment-alt text-2xl" />
				<div
					contenteditable
					spellcheck="false"
					class="text-xl bg-transparent ml-3"
					bind:textContent={chattingWith}
				>
					{chattingWith}
				</div>
			</section>
			<section
				class="flex flex-col h-[500px] bg-white rounded-b-md overflow-y-scroll"
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
								class="bg-gray-200 rounded-lg mr-2 p-3 text-left"
							>
								{content}
							</div>
							<div
								class="avatar bg-blue-500 mr-4"
								on:click={() => (ownMessage = !ownMessage)}
							>
								<i class="fas fa-user" />
							</div>
						</div>
					{:else}
						<div
							transition:fly={{ x: -200, duration: 400 }}
							class="flex ml-4 mt-4"
						>
							<div
								class="avatar bg-purple-500"
								on:click={() => (ownMessage = !ownMessage)}
							>
								{initials}
							</div>
							<div
								contenteditable
								spellcheck="false"
								on:contextmenu={(e) => deleteMessage(e, i)}
								on:input={(e) =>
									(messages[i].content = e.target.innerText)}
								class="bg-gray-200 rounded-lg ml-2 p-3 text-left"
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
		@apply grid place-items-center text-white w-10 h-10 rounded-full cursor-pointer;
	}
</style>
