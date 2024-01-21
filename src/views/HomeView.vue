<template>
    <div
        class="full-screen-overlay"
        v-if="showOverlay"
    >
        <span
            v-if="loading"
            class="loader"
        ></span>
        
        <div
            class="game-type-chooser"
            v-if="!gameType"
        >
            <div class="game-type-cards">
                <div
                    v-for="game in EGameType"
                    class="game-card"
                    :key="game"
                    @click="handleGameCardClick(game)"
                >
                <img v-if="game === EGameType.PlayerVsPlayer" src="../assets/human.jpeg" alt="Player 1" class="icon">

<!-- Left computer image -->
<img v-else-if="game === EGameType.PlayerVsComputer" src="../assets/human.jpeg" alt="Computer" class="icon">

<!-- Left computer vs right computer images -->
<div v-else>
    <img src="../assets/easy.jpeg" alt="Computer 1" class="icon">
   
    <img src="../assets/easy.jpeg" alt="Computer 2" class="icon">
</div>


<!-- Right player image -->
<img v-if="game === EGameType.PlayerVsPlayer" src="../assets/human.jpeg" alt="Player 2" class="icon">

<!-- Right computer image -->
<img v-else-if="game === EGameType.PlayerVsComputer" src="../assets/easy.jpeg" alt="Computer" class="icon">
                </div>
            </div>
        </div>

        <div
            v-else-if="showDifficutlyChooser"
            class="difficulty-chooser"
        >
            <div class="player-difficulty">
                {{ showWhiteDifficultyChooser ? "White player" : "Black player" }}
            </div>

            <div
                class="difficulty-cards"
                v-if="showWhiteDifficultyChooser"
            >
                <div
                    v-for="diff in EDifficulty"
                    :key="diff"
                    class="difficulty-card"
                    @click="handleDifficultyClick('white', diff)"
                >
                <img v-if="diff === 'easy'" src="../assets/easy.jpeg" alt="Easy" class="difficulty-icon">
            <img v-else-if="diff === 'medium'" src="../assets/medium.jpeg" alt="Medium" class="difficulty-icon">
            <img v-else-if="diff === 'hard'" src="../assets/hard.jpeg" alt="Hard" class="difficulty-icon">
                </div>
            </div>

            <div
                class="difficulty-cards"
                v-if="!showWhiteDifficultyChooser && showBlackDifficultyChooser"
            >
                <div
                    v-for="diff in EDifficulty"
                    :key="diff"
                    class="difficulty-card"
                    @click="handleDifficultyClick('black', diff)"
                >
                <img v-if="diff === 'easy'" src="../assets/easy.jpeg" alt="Easy" class="difficulty-icon">
            <img v-else-if="diff === 'medium'" src="../assets/medium.jpeg" alt="Medium" class="difficulty-icon">
            <img v-else-if="diff === 'hard'" src="../assets/hard.jpeg" alt="Hard" class="difficulty-icon">
                </div>
            </div>
        </div>
    </div>

    <div id="home">
        <div class="left-section">
      <!-- Display human and AI images based on game type -->
      <div v-if="gameType === EGameType.PlayerVsComputer" class="player-images">
        <div class="player-item">
          <img src="../assets/human.jpeg" alt="Human" class="player-icon">
          <p>Human Player</p>
          <p v-if=" gameState.unplacedPieces.white !== 0" > Unplaced pieces:</p>
          <p v-if=" gameState.unplacedPieces.white !== 0"> {{ gameState.unplacedPieces.white }}</p>
          <p v-if="message" class="mill-message">{{ message }}</p>
        </div>
        <div class="player-item">
          <img src="../assets/hard.jpeg" alt="AI" class="player-icon">
          <p>AI Player</p>
          <p v-if=" gameState.unplacedPieces.black !== 0" > Unplaced pieces:</p>
          <p v-if=" gameState.unplacedPieces.black !== 0"> {{ gameState.unplacedPieces.black }}</p>
          <p v-if="message2" class="mill-message">{{ message2 }}</p>
        </div>
      </div>

      <!-- Display AI images for both players if game type is ComputerVsComputer -->
      <div v-else-if="gameType === EGameType.ComputerVsComputer" class="player-images">
        <div class="player-item">
          <img src="../assets/hard.jpeg" alt="Computer 1" class="player-icon">
          <p>Computer 1</p>
          <p v-if=" gameState.unplacedPieces.white !== 0" > Unplaced pieces:</p>
          <p v-if=" gameState.unplacedPieces.white !== 0"> {{ gameState.unplacedPieces.white }}</p>
          <p v-if="message" class="mill-message">{{ message }}</p>
        </div>
        <div class="player-item">
          <img src="../assets/hard.jpeg" alt="Computer 2" class="player-icon">
          <p>Computer 2</p>
          <p v-if=" gameState.unplacedPieces.black !== 0" > Unplaced pieces:</p>
          <p v-if=" gameState.unplacedPieces.black !== 0"> {{ gameState.unplacedPieces.black }}</p>
          <p v-if="message2" class="mill-message">{{ message2 }}</p>
        </div>
      </div>
      <div v-else-if="gameType === EGameType.PlayerVsPlayer" class="player-images">
        <div class="player-item">
          <img src="../assets/human.jpeg" alt="Computer 1" class="player-icon">
          <p>Player 1</p>
          <p v-if=" gameState.unplacedPieces.white !== 0" > Unplaced pieces:</p>
          <p v-if=" gameState.unplacedPieces.white !== 0"> {{ gameState.unplacedPieces.white }}</p>
          <p v-if="message" class="mill-message">{{ message }}</p>
        </div>
        <div class="player-item">
          <img src="../assets/human.jpeg" alt="Computer 2" class="player-icon">
          <p>Player 2</p>
          <p v-if=" gameState.unplacedPieces.black !== 0" > Unplaced pieces:</p>
          <p v-if=" gameState.unplacedPieces.black !== 0"> {{ gameState.unplacedPieces.black }}</p>
          <p v-if="message2" class="mill-message">{{ message2 }}</p>
        </div>
      </div>

      <!-- Add some space for text -->
    </div>
        <div class="floating-notifications">
            <!-- Mill formation message -->
            <div  class="mill-formation-message">
                <p class="current-player">
            {{ getCurrentPlayerText }}
        </p>
        <div class="mill-formation-message" v-if="millFormationMessage">
    {{ millFormationMessage }}
</div>
            </div>
          
        </div>
        
        <div class="mica-container">
           
            <div
                class="mica"
                ref="micaRef"
                v-resize="calculateGap"
            >
                <div
                    class="point"
                    v-for="point in calculatedPoints"
                    :key="`point-${point.x}-${point.y}`"
                    :class="{
                        nearby: nearbyFreePoints.includes(point.point),
                    }"
                    :style="{
                        left: point.x * micaGap + 'px',
                        top: point.y * micaGap + 'px',
                    }"
                    @click="handlePointClick(point.point)"
                >
                    <div class="point-inner"></div>
                </div>

                <div
                    class="connection"
                    v-for="connection in calculatedConnections"
                    :key="`connection-${connection.x1}-${connection.y1}-${connection.x2}-${connection.y2}`"
                    :style="{
                        left: connection.x1 * micaGap + 'px',
                        top: connection.y1 * micaGap + 'px',
                        width: (connection.x2 - connection.x1) * micaGap + 'px',
                        height: (connection.y2 - connection.y1) * micaGap + 'px',
                    }"
                ></div>

                <div
                    v-for="figure in calculatedFigures"
                    class="figure"
                    :class="{
                        selected: figure.point === selectedFigure,
                        black: figure.color === 'black',
                        white: figure.color === 'white',
                        canRemove: canRemoveOpponentsPiece && opponentsStones.includes(figure.point),
                        isInFormedMill: formedMillStones.includes(figure.point) && !(canRemoveOpponentsPiece && opponentsStones.includes(figure.point)),
                    }"
                    :style="{
                        left: figure.x * micaGap + 'px',
                        top: figure.y * micaGap + 'px',
                    }"
                    :key="`figure-${figure.x}-${figure.y}`"
                    v-click-outside="() => selectFigure(null)"
                    @click.stop="selectFigure(figure.point)"
                ></div>
            </div>
        </div>
    </div>
</template>

<script setup lang="ts">
    import { ref, onMounted, computed, watch } from "vue";
    import Client, { EDifficulty, type IGameState, type IMapObject, type ITakenPoint, type IPoint, type TPlayer } from "@/Client";
    import { vResize, vClickOutside } from "../vueDirectives";
    import axios from "axios";

    interface ICalculatedFigure {
        x: number;
        y: number;
        color: "black" | "white";
        point: ITakenPoint;
    }

    interface ICalculatedPoint {
        point: string;
        x: number;
        y: number;
    }

    interface ICalculatedConnection {
        x1: number;
        y1: number;
        x2: number;
        y2: number;
    }

    interface IPlayerData {
        name: string;
        color: TPlayer;
        timeout?: number;
        difficulty: EDifficulty | null;
        depth: number;
    }

    enum EGameType {
        PlayerVsPlayer = "Player vs Player",
        PlayerVsComputer = "Player vs Computer",
        ComputerVsComputer = "Computer vs Computer",
    }

    const gameState = ref<IGameState>({
        player: "white",
        unplacedPieces: {
            black: 9,
            white: 9,
        },
        occupiedPoints: [],
    });

    const gameType = ref<EGameType | null>(null);

    const loading = ref<boolean>(false);

    const mapsData = ref<Array<IMapObject> | null>(null);
    const selectedMap = ref<IMapObject | null>(null);
    const micaSize = ref<number | null>(null);

    const micaRef = ref<HTMLElement | null>(null);
    const micaGap = ref<number>(0);

    const selectedFigure = ref<ITakenPoint | null>(null);

    const canRemoveOpponentsPiece = ref<boolean>(false);

    const whitePlayer = ref<IPlayerData>({
        name: "White player",
        color: "white",
        timeout: 30,
        difficulty: null,
        depth: 4,
    });

    const blackPlayer = ref<IPlayerData>({
        name: "Black player",
        color: "black",
        timeout: 30,
        difficulty: null,
        depth: 4,
    });

    const showOverlay = computed(() => {
        if (!gameType.value) return true;

        switch (gameType.value) {
            case EGameType.PlayerVsPlayer:
                if (loading.value) return true;
                break;
            case EGameType.PlayerVsComputer:
                if (loading.value) return true;
                if (showBlackDifficultyChooser.value) return true;
                break;
            case EGameType.ComputerVsComputer:
                if (showBlackDifficultyChooser.value || showWhiteDifficultyChooser.value) return true;
                break;
        }

        return false;
    });

    const showDifficutlyChooser = computed(() => {
        if (gameType.value === EGameType.PlayerVsPlayer) return false;
        if (!showBlackDifficultyChooser.value && !showWhiteDifficultyChooser.value) return false;
        return true;
    });

    const showBlackDifficultyChooser = computed(() => {
        if (gameType.value === EGameType.PlayerVsPlayer) return false;
        return blackPlayer.value.difficulty === null;
    });

    const showWhiteDifficultyChooser = computed(() => {
        if (gameType.value === EGameType.PlayerVsPlayer || gameType.value === EGameType.PlayerVsComputer) return false;
        return whitePlayer.value.difficulty === null;
    });

    const opponentsStones = computed(() => {
        if (!gameState.value) return [];

        const stones = gameState.value.occupiedPoints.filter((point) => {
            // if not opponent player - skip
            if (point.player === gameState.value.player) return false;

            // if part of mill - skip
            if (isInFormedMill(point)) {
                return false;
            }

            return true;
        });

        // If no stones - return all opponent stones
        if (stones.length === 0) {
            return gameState.value.occupiedPoints.filter((point) => point.player !== gameState.value.player);
        }

        return stones;
    });

    const formedMillStones = computed(() => {
        return gameState.value.occupiedPoints.filter((point) => isInFormedMill(point));
    });

    const nearbyFreePoints = computed(() => {
        if (selectedFigure.value === null) return [];
        if (!selectedMap.value) return [];

        const points: Array<String> = [];

        if (countPieces(gameState.value.player) === 3) {
            for (const point of selectedMap.value.map_data.points) {
                if (!gameState.value.occupiedPoints.find((occupied_point) => occupied_point.point === point)) {
                    points.push(point);
                }
            }
        } else {
            for (let index = 0; index < selectedMap.value.map_data.connections.length; index++) {
                const connection = selectedMap.value.map_data.connections[index];

                if (connection[0] === selectedFigure.value.point) {
                    for (let index = 1; index < connection.length; index++) {
                        // If point is occupied by other player figure - skip
                        if (gameState.value.occupiedPoints.find((occupied_point) => occupied_point.point === connection[index])) continue;

                        const point = connection[index];
                        points.push(point);
                    }
                }
            }
        }

        return points;
    });
    const getCurrentPlayerText = computed(() => {
        if (!gameState.value) return '';

        return `Current Player: ${gameState.value.player === 'white' ? 'White' : 'Black'}`;
    });
    const mills = computed(() => {
        const mills = new Map<String, Array<Array<String>>>();

        if (!selectedMap.value) return mills;

        for (const point of selectedMap.value.map_data.points) {
            mills.set(point, []);
        }
        for (const mill of selectedMap.value.map_data["mills"]) {
            if (mill.length === 3) {
                // A mill is formed by 3 points
                for (const point of mill) {
                    mills.get(point)?.push(mill);
                }
            }
        }

        return mills;
    });

    const calculatedPoints = computed<Array<ICalculatedPoint>>(() => {
        if (!selectedMap.value) return [];

        const points = [];

        for (let index = 0; index < selectedMap.value.map_data.points.length; index++) {
            const point = selectedMap.value.map_data.points[index];

            const coords = normalizeChessCord(separateChessCoordToXY(point)!);
            if (!coords) continue;

            points.push({
                point: point,
                x: coords.x,
                y: coords.y,
            });
        }

        return points;
    });

    const calculatedConnections = computed<Array<ICalculatedConnection>>(() => {
        if (!selectedMap.value) return [];

        const connections = [];

        for (let index = 0; index < selectedMap.value.map_data.connections.length; index++) {
            const connection = selectedMap.value.map_data.connections[index];
            if (typeof connection[0] === "number") continue;

            const coords1 = normalizeChessCord(separateChessCoordToXY(connection[0])!);
            if (!coords1) continue;

            let coords2;

            for (let index = 1; index < connection.length; index++) {
                const point = connection[index];
                if (typeof point === "number") continue;

                coords2 = normalizeChessCord(separateChessCoordToXY(point)!);
                if (!coords2) continue;

                connections.push({
                    x1: coords1.x,
                    y1: coords1.y,
                    x2: coords2.x,
                    y2: coords2.y,
                });
            }
        }

        return connections;
    });

    const calculatedFigures = computed<Array<ICalculatedFigure>>(() => {
        if (!gameState.value) return [];

        const figures = [];

        for (let index = 0; index < gameState.value.occupiedPoints.length; index++) {
            const point = gameState.value.occupiedPoints[index];

            const coords = normalizeChessCord(separateChessCoordToXY(point.point)!);
            if (!coords) continue;

            figures.push({
                x: coords.x,
                y: coords.y,
                color: point.player,
                point: point,
            });
        }

        return figures;
    });
    const millFormationMessage = ref<string | null>(null);
    const message = ref<string | null>(null);
    const message2 = ref<string | null>(null);

    

    function handlePointClick(point: String) {
        if (canRemoveOpponentsPiece.value) return;

        // If can place figure - place it
        if (gameType.value !== EGameType.ComputerVsComputer && gameState.value.unplacedPieces[gameState.value.player] > 0) {
            gameState.value.unplacedPieces[gameState.value.player]--;
            placeFigure(point);
        } else {
            if (!selectedFigure.value) return;
            if (!nearbyFreePoints.value.includes(point)) return;
            selectedFigure.value.point = point;
        }

        selectedFigure.value = null;

        if (isInFormedMill({ point: point, player: gameState.value.player })) {
            canRemoveOpponentsPiece.value = true;
            millFormationMessage.value = `${gameState.value.player === 'white' ? 'White' : 'Black'} player formed a mill!`;
            if(gameState.value.player == 'white')
            {message.value = `I have to eat!`;
            message2.value = "Oh i got eaten"}
            
            if(gameState.value.player == 'black'){
             message2.value = `I have to eat!`
            message.value = "Oh i got eaten"
            }
            
            
        // Optionally, you can add a timeout to hide the message after some time
        setTimeout(() => {
            millFormationMessage.value = null;
        }, 3000);
        } else {
            millFormationMessage.value = null;
            canRemoveOpponentsPiece.value = false;
            message.value = null;
            message.value = null;
            setOpponentPlayer();
        }
        
    }

    function setOpponentPlayer() {
        if (gameState.value.player === "white") {
            gameState.value.player = "black";
        } else {
            gameState.value.player = "white";
        }
    }

    function placeFigure(point: String) {
        gameState.value.occupiedPoints.push({
            point: point,
            player: gameState.value.player,
        });
    }

    function isInFormedMill(point: ITakenPoint) {
        if (!mills.value) return false;

        const formedMills = mills.value.get(point.point);
        if (!formedMills) return false;

        for (const mill of formedMills) {
            let count = 0;
            for (const millPoint of mill) {
                if (gameState.value.occupiedPoints.find((occupied_point) => occupied_point.point === millPoint && occupied_point.player === point.player)) {
                    count++;
                }
            }
            if (count === 3) {
                console.log("FORMED MILL", mill);
                return true;
            }
        }
        return false;
    }

    function countPieces(player: TPlayer) {
        return gameState.value.occupiedPoints.filter((point) => point.player === player).length + gameState.value.unplacedPieces[player];
    }

    function selectFigure(figure: ITakenPoint | null) {
        if (loading.value) return;

        if (!figure) {
            selectedFigure.value = null;
            return;
        }

        if (canRemoveOpponentsPiece.value && opponentsStones.value.includes(figure)) {
            canRemoveOpponentsPiece.value = false;

            gameState.value.occupiedPoints = gameState.value.occupiedPoints.filter((occupied_point) => {
                return occupied_point.point !== figure.point;
            });

            setOpponentPlayer();
        } else {
            if (gameState.value.unplacedPieces[gameState.value.player] > 0) return;
            if (figure.player !== gameState.value.player) return;
            selectedFigure.value = figure;
        }
    }

    function calculateGap(width?: number, height?: number) {
        if (!micaSize.value || !micaRef.value) return 0;
        const size = width ? width : getContainerSize();
        micaGap.value = size / (micaSize.value - 1);
    }

    function separateChessCoordToXY(coord: String): IPoint | null {
        const regArray = coord.match(/([a-zA-Z]+)(\d+)/);
        if (!regArray) return null;

        const [letters, numbers] = regArray.slice(1);
        const letterAsNumber = letters.toLowerCase().charCodeAt(0) - "a".charCodeAt(0);

        return {
            x: letterAsNumber,
            y: parseInt(numbers) - 1,
        };
    }

    function normalizeChessCord(coord: IPoint): IPoint {
        return {
            x: coord.x,
            y: getMicaSize() - coord.y - 1,
        };
    }

    function getMicaSize(): number {
        if (!selectedMap.value) return 0;

        let max = 0;

        for (let index = 0; index < selectedMap.value.map_data.points.length; index++) {
            const point = selectedMap.value.map_data.points[index];

            const coords = separateChessCoordToXY(point);
            if (!coords) continue;

            if (coords.x > max) max = coords.x;
            if (coords.y > max) max = coords.y;
        }

        return max + 1;
    }

    function getContainerSize() {
        if (!micaRef.value) return 0;
        return micaRef.value.clientWidth;
    }

    function handleDifficultyClick(player: TPlayer, difficulty: EDifficulty) {
        if (player === "white") {
            whitePlayer.value.difficulty = difficulty;
        } else {
            blackPlayer.value.difficulty = difficulty;

            if (gameType.value === EGameType.ComputerVsComputer) {
                makeMove();
            }
        }
    }

    async function makeMove() {
        if (!selectedMap.value) return;

        loading.value = true;

        const difficulty = gameState.value.player === "white" ? whitePlayer.value.difficulty : blackPlayer.value.difficulty;
        let depth: number;
    let timeout: number;

    switch (difficulty) {
      case "easy":
        depth = 2;
        timeout = 10;
        break;
      case "medium":
        depth = 5;
        timeout = 20;
        break;
      case "hard":
        depth = 7;
        timeout = 30;
        break;
      default:
        depth = 4;
        timeout = 20;
    }
    
    console.log(depth)
    console.log(timeout)
        try {
            gameState.value = await Client.calculateMove(
                {
                    mapName: selectedMap.value.map_name,
                    timeout: timeout,
                    difficulty: difficulty ?? EDifficulty.Easy,
                    depth: depth,
                    gameState: gameState.value,
                }
            );
        } catch (error) {
            if (axios.isCancel(error)) {
                console.log("Request canceled: ", error.message);
            } else {
                console.error("Calculate move error: ", error);
            }
        }

        loading.value = false;
    }

    function setSelectedMap(map: IMapObject) {
        selectedMap.value = map;

        micaSize.value = getMicaSize();
        calculateGap();
    }

    function handleGameCardClick(game: EGameType) {
        gameType.value = game;
    }

    function resetGame() {
        Client.cancelRequest("Game reset");

        gameState.value = {
            player: "white",
            unplacedPieces: {
                black: 9,
                white: 9,
            },
            occupiedPoints: [],
        };

        canRemoveOpponentsPiece.value = false;
        selectedFigure.value = null;
        gameType.value = null;
        loading.value = false;
        whitePlayer.value.difficulty = null;
        blackPlayer.value.difficulty = null;
    }

    function setupKeyboardShortcuts() {
        window.addEventListener("keydown", (event) => {
            if (event.key === "Escape") {
                event.preventDefault();
                resetGame();
            }
        });
    }

    watch(
        gameState,
        async (newValue: IGameState, oldValue: IGameState) => {
            if (!newValue) return;

            const blackStones = countPieces("black");
            const whiteStones = countPieces("white");

            // REFACTOR - Make it pretty
            if (blackStones === 2) {
                alert("White player won!");
            } else if (whiteStones === 2) {
                alert("Black player won!");
            }

            // Reset game if 2 stones left
            if (blackStones === 2 || whiteStones === 2) {
                resetGame();
                return;
            }

            if (gameType.value === EGameType.ComputerVsComputer) {
                makeMove();
            } else if (gameType.value === EGameType.PlayerVsComputer) {
                if (newValue.player === "black") {
                    makeMove();
                }
            }
        },
        { deep: true }
    );

    onMounted(async () => {
        mapsData.value = await Client.maps();

        if (mapsData.value && mapsData.value.length > 0) {
            setSelectedMap(mapsData.value[0]);
        }

        setupKeyboardShortcuts();
    });

</script>

<style scoped lang="scss">
    @mixin flex($direction: row, $justify: center, $align: center) {
        display: flex;
        flex-direction: $direction;
        justify-content: $justify;
        align-items: $align;
    }

    @mixin full-screen {
        width: 100vw;
        height: 100vh;
    }

    @mixin full {
        width: 100%;
        height: 100%;
    }

    @mixin shadow {
        box-shadow: 0px 0px 10px 0px rgba(0, 0, 0, 0.75);
    }

    @mixin padding($padding: 40px) {
        padding: $padding;
    }

    @mixin backdrop-filter {
        backdrop-filter: blur(10px);
    }

    @mixin border-radius($radius: 10px) {
        border-radius: $radius;
    }

    @mixin border-rounded {
        border-radius: 50%;
    }

    @mixin transition($transition: all) {
        transition: $transition 100ms ease-in-out;
    }

    @mixin translateCenter {
        position: absolute;
        transform: translate(-50%, -50%);
    }

    #home {
        @include flex();
        @include full();
        @include padding();

        background-color: rgb(225, 196, 144);

        .mica-container {
            @include border-radius();
            @include padding();

            background-color: rgb(225, 196, 144);

            .mica {
                @include full();

                position: relative;
                .point {
                    @include border-radius(50%);
                    @include transition(background-color);
                    @include flex();
                    @include translateCenter();

                    width: 48px;
                    height: 48px;

                    z-index: 10;

                    cursor: pointer;

                    .point-inner {
                        @include border-rounded();
                        @include transition();

                        width: 16px;
                        height: 16px;
                        background-color: rgb(69, 69, 69);
                    }

                    &.nearby {
                        .point-inner {
                            transform: scale(1.2);
                            background-color: rgb(21, 143, 80);
                        }

                        &:hover {
                            .point-inner {
                                background-color: rgb(21, 143, 80);
                            }
                        }

                        &:active {
                            .point-inner {
                                background-color: rgb(21, 143, 80);
                            }
                        }
                    }

                    &:hover {
                        .point-inner {
                            background-color: rgb(96, 96, 96);
                        }
                    }

                    &:active {
                        .point-inner {
                            background-color: rgb(48, 48, 48);
                        }
                    }
                }

                .figure {
                    @include shadow();
                    @include backdrop-filter();
                    @include transition();
                    @include border-rounded();

                    position: absolute;
                    transform: translate(-50%, -50%);

                    width: 48px;
                    height: 48px;

                    z-index: 20;

                    cursor: pointer;

                    &.selected {
                        transform: translate(-50%, -50%) scale(1.2);
                    }

                    &.black {
                        background-color: rgb(0, 0, 0);

                        &.isInFormedMill {
                            background-color: rgba(73, 255, 6, 0.5);
                        }

                        &.canRemove {
                            background-color: rgba(255, 0, 0, 0.5);
                        }
                    }

                    &.white {
                        background-color: rgb(255, 255, 255);

                        &.isInFormedMill {
                            background-color: rgba(209, 233, 0, 0.942);
                        }

                        &.canRemove {
                            background-color: rgba(255, 0, 0, 0.5);
                        }
                    }

                    &:hover {
                        transform: brightness(1.2);
                    }

                    &:active {
                        transform: brightness(0.8);
                    }
                }

                .connection {
                    position: absolute;
                    border: 2px solid gray;

                    translate: -2px -2px;
                }
            }
        }
    }

    .full-screen-overlay {
        @include flex();
        @include full-screen();

        position: fixed;
        top: 0;
        left: 0;
        z-index: 9999;

        background-color: rgba(0, 0, 0, 0.25);
    }

    .game-type-chooser {
        @include flex();
        @include full();

        .game-type-cards {
            @include flex();

            gap: 20px;

            .game-card {
                @include shadow();
                @include backdrop-filter();
                @include border-radius();
                @include flex();
                @include transition();

                width: 200px;
                height: 200px;

                background-color: rgb(225, 196, 144);

                cursor: pointer;

                &:hover {
                    transform: scale(1.05);
                }

                &:active {
                    transform: scale(0.95);
                }
            }
        }
    }
    .mill-formation-message {
    font-size: 18px;
    font-weight: bold;
    color: #4CAF50; /* Green color, adjust as needed */
    margin-top: 10px;
    text-align: center;
    /* Add any additional styling as needed */
}
    .difficulty-chooser {
        @include flex(column);
        @include full();

        .player-difficulty {
            font-size: 24px;
            font-weight: bold;
            margin-bottom: 20px;
        }

        .difficulty-cards {
            @include flex();

            gap: 20px;

            .difficulty-card {
                @include shadow();
                @include backdrop-filter();
                @include border-radius();
                @include flex();
                @include transition();

                width: 200px;
                height: 200px;

                background-color: rgb(225, 196, 144);

                text-transform: capitalize;

                cursor: pointer;

                &:hover {
                    transform: scale(1.05);
                }

                &:active {
                    transform: scale(0.95);
                }
            }
        }
    }

    .loader {
        width: 48px;
        height: 48px;
        border: 5px solid rgba($color: #ffffff, $alpha: 0.75);
        border-bottom-color: transparent;
        border-radius: 50%;
        display: inline-block;
        box-sizing: border-box;
        animation: rotation 1s linear infinite;
    }

    @keyframes rotation {
        0% {
            transform: rotate(0deg);
        }
        100% {
            transform: rotate(360deg);
        }
    }

    // Responsive CSS
    @media (max-aspect-ratio: 1/1) {
        .mica-container {
            width: 100%;
            aspect-ratio: 1;
        }
    }

    @media (min-aspect-ratio: 1/1) {
        .mica-container {
            height: 100%;
            aspect-ratio: 1;
        }
    }

    @media (max-width: 540px) {
        .figure {
            width: 32px !important;
            height: 32px !important;
        }

        .point {
            width: 32px !important;
            height: 32px !important;

            .point-inner {
                width: 12px !important;
                height: 12px !important;
            }
        }
    }
    .icon {
        width: 50px;  // Adjust the width as needed
        height: 50px; // Adjust the height as needed
        margin-right: 5px; // Adjust spacing as needed
    }

    .computer-vs {
        display: flex;
        align-items: center;

        span {
            margin: 0 5px;
        }
    }
    .difficulty-icon {
        width: 170px;  // Adjust the width as needed
        height: 170px; // Adjust the height as needed
        // Add any additional styling as needed
    }

    .difficulty-cards {
        @include flex();

        gap: 20px;

        .difficulty-card {
            @include shadow();
            @include backdrop-filter();
            @include border-radius();
            @include flex();
            @include transition();

            width: 200px;
            height: 200px;

            background-color: rgb(225, 196, 144);

            cursor: pointer;

            &:hover {
                transform: scale(1.05);
            }

            &:active {
                transform: scale(0.95);
            }
        }
    }
    .current-player {
    font-size: 24px;
    font-weight: bold;
    color: #333; /* Set the desired text color */
    margin-bottom: 10px; /* Add some space below the text */
    text-transform: uppercase; /* Convert text to uppercase */
    letter-spacing: 1px; /* Adjust letter spacing for style */
    text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3); /* Add a subtle text shadow */
    
    /* Move the background styling here */
    /* Background gradient with animation for a dynamic look */
   
    /* Border for added emphasis */
   
    /* Padding for better visual appeal */
  

    /* Animation for a subtle effect */
    animation: glow 2s ease-in-out infinite;
}

/* Add the keyframes for the animations */
@keyframes gradientAnimation {
    0% {
        background-position: 0% 50%;
    }
    50% {
        background-position: 100% 50%;
    }
    100% {
        background-position: 0% 50%;
    }
}

@keyframes glow {
    0%, 100% {
        text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
    }
    50% {
        text-shadow: 4px 4px 8px rgba(0, 0, 0, 0.5);
    }
}
.floating-notifications {
    position: fixed;
    top: 20px;
    right: 20px;
    max-width: 300px;
}

.mill-formation-message, .current-player {
    background-color: #fff;
    border: 1px solid #ddd;
    padding: 10px;
    margin-bottom: 10px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.mill-formation-message {
    color: #4CAF50; /* Green color for mill formation message */
}

.current-player {
    color: #2196F3; /* Blue color for current player text */
}
.left-section {
  position: absolute;
  top: 50%;
  left: 0; /* Position at the left edge of the screen */
  transform: translateY(-50%);
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 10px; /* Add padding for better spacing */
}

.player-images {
  display: flex;
  justify-content: space-between; /* Adjust spacing between images */
  width: 100%; /* Make the row take full width */
}

.player-item {
  text-align: center;
  border: 2px solid #333; /* Add border */
  padding: 10px; /* Add padding for better spacing */
  border-radius: 8px; /* Add border-radius for rounded corners */
  transition: transform 0.3s ease-in-out; /* Add transition effect */
}

.player-item:hover {
  transform: scale(1.1); /* Add scale effect on hover */
}

.player-icon {
  width: 120px; /* Adjust width as needed */
  height: 120px; /* Adjust height as needed */
  border-radius: 50%; /* Add border-radius for rounded shape */
}

.player-name {
  margin-top: 10px; /* Add space between image and name */
  font-size: 16px; /* Adjust font size as needed */
}

.text-space {
  margin-top: 20px; /* Add space between images and text */
}

.text-message {
  font-size: 18px; /* Adjust font size as needed */
  font-weight: bold; /* Add bold font weight */
}

/* Styling for the main game section */
.main-game-section {
  margin-left: 160px; /* Adjust the margin to create space between the left section and the main game */
}
.mill-message {
  font-size: 14px; /* Adjust font size as needed */
  font-weight: bold; /* Add bold font weight */
  color: #FF0000; /* Red color for mill message */
  margin-top: 5px; /* Add space between player name and message */
}
</style>
