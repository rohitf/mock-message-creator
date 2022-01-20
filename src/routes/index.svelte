<script lang="ts">
	let otherPerson = "The General";
	$: initials = otherPerson
		.split(" ")
		.map((s) => s.charAt(0).toUpperCase())
		.join("");

	interface Message {
		ownMessage: boolean;
		content: string;
	}

	let messages: Message[] = [
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

	function addMessage() {
		console.log("clicked dbl");
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

<body
	class="w-screen h-screen absolute left-0 top-0 grid place-items-center text-center p-4"
>
	<main class="w-fit rounded-md shadow-md">
		<section class="header flex justify-center w-96 p-4 rounded-t-md">
			<i class="fas fa-comment-alt text-2xl" />
			<div
				contenteditable
				class="text-xl bg-transparent ml-3"
				bind:textContent={otherPerson}
			>
				{otherPerson}
			</div>
		</section>
		<section
			class="flex flex-col h-[500px] bg-white rounded-b-md overflow-y-scroll"
			on:dblclick={addMessage}
		>
			{#each messages as { ownMessage, content }, i}
				{#if ownMessage}
					<div class="flex mt-4 justify-end">
						<div
							contenteditable
							on:contextmenu={(e) => deleteMessage(e, i)}
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
					<div class="flex ml-4 mt-4">
						<div
							class="avatar bg-purple-500"
							on:click={() => (ownMessage = !ownMessage)}
						>
							{initials}
						</div>
						<div
							contenteditable
							on:contextmenu={(e) => deleteMessage(e, i)}
							class="bg-gray-200 rounded-lg ml-2 p-3 text-left"
						>
							{content}
						</div>
					</div>
				{/if}
			{/each}
		</section>
	</main>
	<h1 class="text-2xl text-gray-700">
		<ul class="list-disc text-left">
			<li>Click name in header to edit</li>
			<li>Double click screen to add message</li>
			<li>Click message to edit</li>
			<li>Click sender icon to switch sender</li>
			<li>Right click message to remove</li>
			<li>Scroll to see more messages</li>
		</ul>
	</h1>
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
