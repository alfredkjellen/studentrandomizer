<script lang="ts">
    import { user, schoolData } from "$lib/firebase";
    import { Student, Class, Seat, Room } from "$lib/classes.ts";
    import { onMount } from "svelte";
    import { browser } from "$app/environment";
    import { svgColor } from "$lib/controller";

    import {exampleClasses} from "$lib/examples.ts"

    let classes:Class [] = [];
  classes = exampleClasses;

let mounted = false;

onMount(() => {
    if (browser) {
        window.addEventListener("click", resetClickedStatus);
        mounted = true;
    }

    return () => {
        if (mounted && browser) {
            window.removeEventListener("click", resetClickedStatus);            
        }
    };
});

    let room2 = new Room("Room 2", [
        [
            new Seat(true),
            new Seat(true),
            new Seat(true),
            new Seat(true),
            new Seat(false),
            new Seat(true),
            new Seat(true),
            new Seat(true),
            new Seat(true),
        ],
        [
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
        ],
        [
            new Seat(true),
            new Seat(true),
            new Seat(true),
            new Seat(true),
            new Seat(false),
            new Seat(true),
            new Seat(true),
            new Seat(true),
            new Seat(true),
        ],
        [
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
        ],
        [
            new Seat(true),
            new Seat(true),
            new Seat(true),
            new Seat(true),
            new Seat(false),
            new Seat(true),
            new Seat(true),
            new Seat(true),
            new Seat(true),
        ],
        [
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
        ],
        [
            new Seat(true),
            new Seat(true),
            new Seat(true),
            new Seat(true),
            new Seat(false),
            new Seat(true),
            new Seat(true),
            new Seat(true),
            new Seat(true),
        ]
    ]);

    let room1 = new Room("Room 1", [
        [
            new Seat(true),
            new Seat(true),
            new Seat(false),
            new Seat(true),
            new Seat(true),
            new Seat(false),
            new Seat(true),
            new Seat(true),
        ],
        [
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
        ],
        [
            new Seat(true),
            new Seat(true),
            new Seat(false),
            new Seat(true),
            new Seat(true),
            new Seat(false),
            new Seat(true),
            new Seat(true),
        ],
        [
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
        ],
        [
            new Seat(true),
            new Seat(true),
            new Seat(false),
            new Seat(true),
            new Seat(true),
            new Seat(false),
            new Seat(true),
            new Seat(true),
        ],
        [
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
        ],
        [
            new Seat(true),
            new Seat(true),
            new Seat(false),
            new Seat(true),
            new Seat(true),
            new Seat(false),
            new Seat(true),
            new Seat(true),
        ],
        [
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
            new Seat(false),
        ],
        [
            new Seat(true),
            new Seat(true),
            new Seat(false),
            new Seat(true),
            new Seat(true),
            new Seat(false),
            new Seat(true),
            new Seat(true),
        ],
    ]);


    let rooms: Room[] = [];

    rooms = [room1, room2];

    let exampleRoom = new Room("Choose room", []);
    let exampleClass = new Class("Choose class", []);

    let currentRoom = exampleRoom;
    let currentClass = exampleClass;

    let allStudents: Student[] = [];
    let randomizedStudents: Student[] = [];

    function shuffleArray(array: any[]) {
        for (let i = array.length - 1; i > 0; i--) {
            const j = Math.floor(Math.random() * (i + 1));
            [array[i], array[j]] = [array[j], array[i]];
        }
        return array;
    }

    function selectRoom(room: Room) {
        currentRoom = room;
        if(currentClass.name !== "Choose class"){
            selectClass(currentClass);
        }
    }

    function selectClass(c: Class) {
        currentClass = c;

        allStudents = c.students.slice().map((student) => new Student(student));

        randomizedStudents = shuffleArray(allStudents.slice());

        createShortNames(c);

        updateRoom();
    }



    function randomizeClass() {

        let presentStudents:Student[] = [];
        

        for(let i = 0; i < currentRoom.layout[0].length; i++){

            for(let j = 0; j < currentRoom.layout.length; j++){

              if(currentRoom.layout[j][i].isAvailable && currentRoom.layout[j][i].student.isPresent && currentRoom.layout[j][i].student.name !== ""){
                presentStudents.push(currentRoom.layout[j][i].student);
              }

            }

          }

        randomizedStudents = shuffleArray(presentStudents.slice());
        updateRoom();
    }




    $: if ($schoolData && $user) {
        classes = $schoolData?.classes ?? [];

        if(classes.length > 0){ 
          classes = sortClasses(classes);
        }


        rooms = [];
        $schoolData.rooms.forEach((room: any) => {
            let array = JSON.parse(room.layout);
            let booleanArray: any[] = [];

            for (let i = 0; i < array.length; i++) {
                booleanArray[i] = [];
                for (let j = 0; j < array[i].length; j++) {
                    booleanArray[i][j] = Boolean(array[i][j]);
                }
            }

            for (var k = 0; k < booleanArray.length; k++) {}

            let seats: Seat[][] = [];
            for (let i = 0; i < booleanArray.length; i++) {
                seats[i] = [];
                for (let j = 0; j < booleanArray[i].length; j++) {
                    seats[i][j] = new Seat(booleanArray[i][j]);
                }
            }
            rooms = [...rooms, new Room(room.name, seats)];
            rooms = sortRooms(rooms);
        });
    }




    function sortClasses(classes: Class[])
    {
      //sort alphabetically so that the rooms get in this order: EK1A, EK1B, EK2A, EK2B, EK3A, EK3B, NA1A, NA1B, NA2A, NA2B, NA3A, NA3B
      classes.sort((a, b) => a.name.localeCompare(b.name));
      return classes
    }

    function sortRooms(rooms: Room[])
    {
      rooms.sort((a, b) => a.name.localeCompare(b.name));
      return rooms
    }




    function resetClickedStatus() {
        currentRoom.layout.forEach((row) => {
            row.forEach((seat) => {
                seat.student.isClicked = false;
            });
        });

        clickedStudent1 = undefined;
        clickedStudent2 = undefined;

        renderRoom();

    }

    

    let clickedStudent1: any = undefined;
    let clickedStudent2: any = undefined;
    let i1: any = 0;
    let j1: any = 0;
    let i2: any = 0;
    let j2: any = 0;

    function handleClick(

        student: Student,
        event: MouseEvent,
        i: number,
        j: number,
    ) {
        event.stopPropagation();
        student.isClicked = !student.isClicked;
        currentRoom.layout[i][j].student = { ...student };

        if (student.isClicked) {
            if (clickedStudent1 === undefined && student.name !== "") {
                clickedStudent1 = student;
                i1 = i;
                j1 = j;
            } else if (clickedStudent2 === undefined && clickedStudent1 !== undefined) {
                clickedStudent2 = student;
                i2 = i;
                j2 = j;
                moveStudents(clickedStudent1, clickedStudent2);
                resetClickedStatus();
            }
        }
    }

    function moveStudents(student1: any, student2: any) {
        let temp = currentRoom.layout[i1][j1].student;
        currentRoom.layout[i1][j1].student = currentRoom.layout[i2][j2].student;
        currentRoom.layout[i2][j2].student = temp;
    }

    function renderRoom() {
        currentRoom.layout = currentRoom.layout.map((row) =>
            row.map((seat) => {
                seat.student = { ...seat.student };
                seat = { ...seat };
                return seat;
            }),
        );
    }

    function updateRoom() {
        let studentList: Student[] = randomizedStudents.slice();

        studentList = studentList.filter(student => student.name !== "");

        
        for (let i = 0; i < currentRoom.layout.length; i++) {
            for (let j = 0; j < currentRoom.layout[i].length; j++) {
                if (
                    currentRoom.layout[i][j].isAvailable &&
                    studentList.length > 0 
                ) {
                    currentRoom.layout[i][j].student = studentList.shift()!;
                } else if (currentRoom.layout[i][j].isAvailable) {
                    currentRoom.layout[i][j].student = new Student("");
                }
            }
        }
    }

    function handlePresense(student: Student) {
        student.isClicked = false;
        
        if (student.isPresent) {
          
            //remove student from room
            for (let i = 0; i < currentRoom.layout.length; i++) {
                for (let j = 0; j < currentRoom.layout[i].length; j++) {
                    if (
                        currentRoom.layout[i][j].student.name === student.name
                    ) {
                        currentRoom.layout[i][j].student = new Student("");
                        allStudents = allStudents.map((s) => {
                            if (s.name === student.name) {
                                s.isPresent = false;
                            }
                            return s;
                        });


                    }
                }
            }

            //fill the gap of the student

            let studentList:any = [];
            for (let i = 0; i < currentRoom.layout.length; i++) {
                for (let j = 0; j < currentRoom.layout[i].length; j++) {
                    if (
                        currentRoom.layout[i][j].isAvailable &&
                        currentRoom.layout[i][j].student.name !== ""
                    ) {
                        studentList.push(currentRoom.layout[i][j].student);
                    }
                }
            }

            for (let i = 0; i < currentRoom.layout.length; i++) {
                for (let j = 0; j < currentRoom.layout[i].length; j++) {
                    if (
                        currentRoom.layout[i][j].isAvailable && studentList.length > 0
                    ) {
                        currentRoom.layout[i][j].student = studentList.shift();
                    }
                    else{
                        currentRoom.layout[i][j].student = new Student("");
                    }
                }
            }




        } else {
            

            let found = false;
            student.isPresent = true;
            for (let i = 0; i < currentRoom.layout.length; i++) {
                for (let j = 0; j < currentRoom.layout[i].length; j++) {
                    if (
                        currentRoom.layout[i][j].isAvailable &&
                        currentRoom.layout[i][j].student.name === "" && !found
                    ) {
                        currentRoom.layout[i][j].student = student;
                        found = true;
                        break;
                    }
                }
            }

            
        }
    }

    //#region Zoom and Size
    
    

    let boxWidth = 0;
    let boxHeight = 0;
    let factor = 0.05;

    let boxFontSize = 30;

    const sizesDict = {
  10: "xs",
  15: "sm",
  20: "xl",
  25: "2xl",
  30: "2xl"
} as const;

type SizeValue = typeof sizesDict[keyof typeof sizesDict];

let textSize: SizeValue = "xl";

// Function to find the closest key in sizesDict
function findClosestSize(fontSize: number): SizeValue {
  const sizes = Object.keys(sizesDict).map(Number);
  const closest = sizes.reduce((prev, curr) => 
    Math.abs(curr - fontSize) < Math.abs(prev - fontSize) ? curr : prev
  );
  return sizesDict[closest as keyof typeof sizesDict];
}

    //scale with screen
    $: 
    {
      if(currentRoom.layout.length > 0){
        boxWidth = innerWidth / currentRoom.layout[0].length;
        boxHeight = (innerHeight - 200) / currentRoom.layout.length;

        boxFontSize = boxWidth / 6.5;

        //get the closest one
        textSize = findClosestSize(boxFontSize);
        
      }
    
    }
    
    function zoom(operation: string) {
        if (currentRoom.name !== "Choose room") {
            if (operation === "+") {
                boxWidth += boxWidth * factor;
                boxHeight += boxHeight * factor;
            } else if (operation === "-") {
                boxWidth -= boxWidth * factor;
                boxHeight -= boxHeight * factor;
            }
        }
    }

   

    //#endregion

    
//#region Selection

    function handleRoomSelection(event:any) {
  const selectedRoomName = event.target.value;
  const selectedRoom = rooms.find(room => room.name === selectedRoomName);
  selectRoom(selectedRoom!);
}



function handleClassSelection(event:any) {
  const selectedClassName = event.target.value;
  const selectedClass = classes.find(c=> c.name === selectedClassName);
  selectClass(selectedClass!);
}

//#endregion


//#region student presense
let isDropdownOpen = false;

  function toggleDropdown() {
    isDropdownOpen = !isDropdownOpen;
  }

  function closeDropdown(event:any) {
    const dropdownMenu = document.getElementById("dropdown-menu")!;
    const dropdownButton = document.getElementById("dropdown-button")!;

    if (
      !dropdownMenu.contains(event.target) &&
      !dropdownButton.contains(event.target)
    ) {
      isDropdownOpen = false;
    }
  }






//#endregion





//#region Full names
let showFullNames = false;
let shortNames: {[key: string]: string} = {};
let firstNameCount: {[key: string]: number} = {};

function createShortNames(c: Class) {
      shortNames = {};
      firstNameCount = {};
      
      // Count occurrences of first names
      c.students.forEach(studentName => {
        const firstName = studentName.split(' ')[0];
        firstNameCount[firstName] = (firstNameCount[firstName] || 0) + 1;
      });

      // Create short names based on first name count
      c.students.forEach(studentName => {
        shortNames[studentName] = getShortName(studentName);
      });
    }
    function getShortName(name: string): string {
      const nameParts = name.trim().split(/\s+/);
      
      if (nameParts.length === 1) {
        return name.trim();
      }

      const firstName = nameParts[0];
      const lastNames = nameParts.slice(1);
      
      // If there's more than one student with this first name, add initials
      if (firstNameCount[firstName] > 1) {
        const initials = lastNames.map(lastName => lastName[0].toUpperCase()).join('.');
        return `${firstName} ${initials}`;
      } else {
        return firstName;
      }
    }


//#endregion




</script>


<style>
    /* Styles for larger screens */
    .menu-container {
      display: flex;
      justify-content: center;
      gap:1rem;
      align-items: center;
      margin-top: 1rem;
    }
  
    /* Styles for smaller screens */
    @media (max-width: 768px) {
      .menu-container {
        flex-direction: column;
        gap: 1rem;
      }
  
      .select {
        width: 100%;
        max-width: none;
      }
  
      .btn-container {
        display: flex;
        gap: 1rem;
        justify-content: center;
      }
    
    }


    .scalable-text {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 100%;
    height: 100%;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }


  </style>
  
  
  <svelte:window on:click={closeDropdown} />
  
  <div class="menu-container">
    <select class="select select-bordered select-sm" on:change={handleRoomSelection}>
      <option disabled selected>Choose room</option>
      {#each rooms as room}
      <option value={room.name}>{room.name}</option>
      {/each}
    </select>
  
    <select class="select select-bordered select-sm" on:change={handleClassSelection}>
      <option disabled selected>Choose class</option>
      {#each classes as c}
      <option value={c.name}>{c.name}</option>
      {/each}
    </select>

  
    <div class="btn-container">
      
      <div class="relative inline-block">
        <button
          id="dropdown-button"
          class="select select-bordered w-56 flex items-center justify-start select-sm"
          on:click={toggleDropdown}
        >
          Students
        </button>
        {#if isDropdownOpen}
        <div
          id="dropdown-menu"
          class="absolute z-10 mt-2 w-full bg-base-200 rounded-md shadow-lg"
        >
          <ul class="py-1 text-sm">
            {#each allStudents as student}
            <li>
              <div class="flex justify-between px-4 py-2">
                {student.name}
                <input
                  type="checkbox"
                  bind:checked={student.isPresent}
                  class="checkbox checkbox-md"
                  on:click={() => handlePresense(student)}
                />
              </div>
            </li>
            {/each}
          </ul>
        </div>
        {/if}
      </div>



      <button
        class="btn btn-sm btn-primary"
        on:click={() => randomizeClass()}
      >
        Randomize
        <svg
          class="w-6 h-6 text-gray-800"
          aria-hidden="true"
          xmlns="http://www.w3.org/2000/svg"
          width="24"
          height="24"
          fill="none"
          viewBox="0 0 24 24"
        >
          <path
            stroke="currentColor"
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            d="M13.484 9.166 15 7h5m0 0-3-3m3 3-3 3M4 17h4l1.577-2.253M4 7h4l7 10h5m0 0-3 3m3-3-3-3"
          />
        </svg>
      </button>

    </div>

    <div class="form-control flex gap-2">
      <label class="label cursor-pointer">
        <span class="label-text">Show full names</span>
        <input type="checkbox" bind:checked={showFullNames} class="checkbox mx-2" />
      </label>
    </div>
  
    <!-- <div class="btn-container">
      <button on:click={() => zoom("+")} class="btn btn-neutral">
        <svg
          class="w-[28px] h-[28px] text-gray-800 dark:text-white"
          aria-hidden="true"
          xmlns="http://www.w3.org/2000/svg"
          width="24"
          height="24"
          fill={$svgColor}
          viewBox="0 0 24 24"
        >
          <path
            fill-rule="evenodd"
            d="M21.707 21.707a1 1 0 0 1-1.414 0l-3.5-3.5a1 1 0 0 1 1.414-1.414l3.5 3.5a1 1 0 0 1 0 1.414ZM2 10a8 8 0 1 1 16 0 8 8 0 0 1-16 0Zm9-3a1 1 0 1 0-2 0v2H7a1 1 0 0 0 0 2h2v2a1 1 0 1 0 2 0v-2h2a1 1 0 1 0 0-2h-2V7Z"
            clip-rule="evenodd"
          />
        </svg>
      </button>
      <button on:click={() => zoom("-")} class="btn btn-neutral">
        <svg
          class="w-[28px] h-[28px] text-gray-800 dark:text-white"
          aria-hidden="true"
          xmlns="http://www.w3.org/2000/svg"
          width="24"
          height="24"
          fill={$svgColor}
          viewBox="0 0 24 24"
        >
          <path
            fill-rule="evenodd"
            d="M21.707 21.707a1 1 0 0 1-1.414 0l-3.5-3.5a1 1 0 0 1 1.414-1.414l3.5 3.5a1 1 0 0 1 0 1.414ZM2 10a8 8 0 1 1 16 0 8 8 0 0 1-16 0Zm4 0a1 1 0 0 0 1 1h6a1 1 0 1 0 0-2H7a1 1 0 0 0-1 1Z"
            clip-rule="evenodd"
          />
        </svg>
      </button>
    </div> -->
  </div>
  
  <div class="divider font-bold">Front of classroom</div>
  
  <div>
    {#each currentRoom.layout as row, i}
    <div class="flex">
      {#each row as box, j}
      {#if box.isAvailable}
      <div class="indicator">
        <div class="indicator-item indicator-top">
          {#if currentRoom.layout[i][j].student.isClicked && currentRoom.layout[i][j].student.name !== ''}
          <button
            on:click={() =>
              handlePresense(currentRoom.layout[i][j].student)}
            class="btn btn-sm btn-circle btn-warning"
            >✕</button
          >
          {/if}
        </div>
  
        <button
          on:click={(event) =>
            handleClick(currentRoom.layout[i][j].student, event, i, j)}
          class={`btn btn-neutral text-${textSize} hover:text-primary hover:border-primary border-2 ${currentRoom.layout[i][j].student.isClicked && currentRoom.layout[i][j].student.name !== "" ? "border-primary text-primary" : ""}`}
          style="width: {boxWidth}px; height: {boxHeight}px;"
        >

        <div style="font-size: {boxFontSize}px;">
          
          {#if showFullNames}          
          {currentRoom.layout[i][j].student.name}
          
          {:else}
          {shortNames[currentRoom.layout[i][j].student.name] || currentRoom.layout[i][j].student.name}
          {/if}
        </div>

        </button>
      </div>
      {:else}
      <div
        class="text-2xl btn btn-disabled"
        style="width: {boxWidth}px; height: {boxHeight}px;"
      ></div>
      {/if}
      {/each}
    </div>
    {/each}
  </div>