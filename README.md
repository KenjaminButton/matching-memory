# Matching Memory:

### Provide the Redux Store to Your Application
Note: [Provider React-Redux documentation](https://react-redux.js.org/api/provider)

1a. In index.js, import the Provider component.
```javascript
import { Provider } from 'react-redux';
```
2a. Implement Provider inside the ReactDOM.render() function.
Note: Pay attention to the comma and its placement.
Note: Pay attention to the store property and its assignment. 
```javascript
ReactDOM.render(
  // Implement Provider component with store below
  // https://react-redux.js.org/api/provider
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);
```

### Create the Board Layout
At the bottom of boardSlice.js:
Note: Take a look at the hint if stuck. It really is helpful.

3a. Create an export statement with a defined selector, selectBoard.
```javascript
export const selectBoard = () => "SOMETHING GOES HERE";
```
3b. The selector should return an array created by state.board.map().
```javascript
export const selectBoard = (state) => state.board.map();
```
3c. The callback function for the map() function should return an object {id: card.id, contents: card.contents}, where card is the argument of the callback function.
```javascript
export const selectorFunction = state => state.selectedData
  .map(object => ({p1: object.p1, p2: object.p2}));
```
The example above shows me that selectorFunction is my selectBoard.
The selectedData is my board.
The argument in my callback is card. Aka object = card in this case. 
p1 is id, p2 is contents.
Note: When returning an object from a single-line arrow function you must wrap the object in parentheses.
```javascript
export const selectBoard = (state) => state.board.map(card => ({id: card.id, contents: card.contents}));
```
4a. In Board.js, start with the necessary import statements.
Import useSelector from react-redux. [React Redux Hooks documentation](https://react-redux.js.org/api/hooks)
```javascript
import { useSelector } from 'react-redux';
```
4b. Import the selector you implemented in the previous step from ./boardSlice.js.
```javascript
import { selectBoard } from './boardSlice.js';
```
5a. Inside the Board component define a variable named currentBoard and assign it the data retrieved from calling useSelector() with the selectBoard selector.
```javascript
const currentBoard = useSelector(selectBoard);
```
6a. To update the row number calculation, reassign the variable numberOfCards to the length of the array of cards, currentBoard.
```javascript
const numberOfCards = currentBoard.length;
```
6b. Inside the utility function getRowCards(), replace the empty object inside rowCards.push() with currentBoard[cardIndex].
```javascript
rowCards.push(currentBoard[cardIndex])
```
![Checkpoint 1](./checkpoint1.png)

### Show Your Cards
In boardSlice.js, implement a selector:

7a. Create an export statement with a defined selector, selectVisibleIDs.
Note: At the bottom of the boardSlice.js file, write the function.
```javascript
export const selectVisibleIDs = () => "Something Here"
```
7b. The selector should return an array that filters the board array and then maps the filtered array. Use state.board.filter().map(). 
```javascript
export const selectVisibleIDs = (state) => state.board.filter().map();
```
7c. The callback function for filter() should test card.visible, where card is the parameter of the callback function.
Note: I google searched filter an array of objects.
[JS Filter for this specific case](https://www.javascripttutorial.net/javascript-array-filter/)
```javascript
export const selectVisibleIDs = (state) => state.board.filter(card => card.visible).map();
// Check if card.visible === true works as well.
export const selectVisibleIDs = (state) => state.board.filter(card => card.visible === true).map();
```
7d. The callback function for map() should return card.id, where card is the parameter of the callback function.
```javascript
export const selectVisibleIDs = (state) => state.board.filter(card => card.visible === true).map(card => card.id);
```
With the selector complete, open Card.js to begin implementing the presentation behavior of the Card component.

In Card.js, start with the necessary imports:

8a. Import useSelector from react-redux.
```javascript
import { useSelector } from 'react-redux';
```
8b. Import the selector you implemented in the previous step from boardSlice.js.
```javascript
import { selectVisibleIDs } from '../../boardSlice';
```
Retrieve the visible card data to know which card to display on the board.

9a. Inside the Card component definition: Define a variable named visibleIDs and assign it the data retrieved from calling useSelector() with the selectVisibleIDs selector.
```javascript
const visibleIDs = useSelector(selectVisibleIDs);
```
10a. Inside the Card component definition: Remove the false in the first if statement. Instead, check that the Card component’s id prop is included in visibleIDs array.
[JS Includes documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/includes)
```javascript
if (visibleIDs.includes(id)) {
  cardText = contents;
  click = () => {};
}
```
You should now see all the cards in their initialized order.
![Checkpoint 2](./checkpoint2.png)

### Start the Game
Open App.js where you will find the function startGameHandler(). You need to dispatch the action from this function.

11a. In App.js, start with imports: Import useDispatch from react-redux.
```javascript
import { useDispatch } from 'react-redux';
```
11b. Import the setBoard action creator from boardSlice.js.
```javascript
import { setBoard } from './features/board/boardSlice.js';
```
Now you can dispatch the action created by setBoard() within startGameHandler(). The setBoard() action creator takes no arguments.

12a. Inside the App component: Define a new variable called dispatch and give it the reference to the Redux store dispatch function.
[React Redux dispatch documentation](https://react-redux.js.org/api/hooks)
```javascript
const dispatch = useDispatch();
```
12b. Inside the startGameHandler() function, dispatch the action created by setBoard().
```javascript
dispatch(setBoard());
```
Note: It is important to remember that action creators don’t dispatch actions but return the formatted action object with the appropriate payload. You can confirm this behavior in boardSlice.js.
When you click the Start Game’ button all the cards will be hidden and their order randomized.

### Flipping Cards
To show the contents of each card the flipCard() action can be dispatched when a card is clicked.
In Card.js, start with the necessary imports:

13a. Add useDispatch to the react-redux import statement.
```javascript
import { useSelector, useDispatch } from 'react-redux';
```
13b. Add flipCard to the boardSlice.js import statement.
```javascript
import { selectVisibleIDs, flipCard } from '../../boardSlice';
```
14a. Inside the Card component definition: Define a variable called dispatch and give it the reference to the Redux store dispatch function returned by useDispatch().
```javascript
const dispatch = useDispatch();
```
14b. Inside the flipHandler() function, dispatch the action created by flipCard(id).
```javascript
dispatch(flipCard(id));
```
Checkpoint 3: When each card is clicked, it becomes ‘flipped’ because it’s visible property is set to true. In fact, you can now flip over all the cards if you want to check that they are random.

Limit the number of visible cards at a time to 2.

15a. Inside the Card component: Place the condition that the length of visibleIDs is equal to 2 in the third if statement.
```javascript
if (visibleIDs.length === 2) {
  click = () => {};
}
```

### Reset the Flipped Cards

In App.js, import and dispatch the resetCards() action:

16a. Add resetCards to the boardSlice.js import statement.
```javascript
import { setBoard, resetCards } from './features/board/boardSlice.js';
```
16b. Inside the tryAgainHandler() function, dispatch the action created by resetCards().
```javascript
dispatch(resetCards())
```
Checkpoint 4: Confirm that you completed this step properly by revealing two cards, and then pressing “Try New Pair”

### Matching Cards

The last step of the game behavior is to identify matched cards on the board using the matched property of each card object in the store. This will require a final selector.

In boardSlice.js:

17a. Create an export statement with a defined selector, selectMatchedIDs.
```javascript
export const selectMatchedIDs = () => "Something Here";
```
17b. This selector is the same as selectVisibleIDs() except you need to change the filter() callback function to test the card.matched value.
```javascript
export const selectMatchedIDs = (state) => state.board.filter(card => card.matched === true).map(card => card.id);
```
In Card.js:

18a. Add selectMatchedIDs to the boardSlice.js import statement.
```javascript
import { selectVisibleIDs, flipCard, selectMatchedIDs } from '../../boardSlice';
```
18b. Define a variable named matchedIDs and assign it the data retrieved from calling useSelector() with the selectMatchedIDs selector.
Note: Place this variable under the previous useSelector call.
```javascript
const matchedIDs = useSelector(selectMatchedIDs);
```

Using the matchedIDs data, you can now reveal the matched cards by changing their cardStyle to 'matched'.

Inside the Card component:

19a. Replace the false condition in the second if statement that only checks if the card id prop is included in matchedIDs.
Note: Check the previous includes usage implemented in the first if statement.
```javascript
// 2nd if statement
// implement card id array membership check
if (matchedIDs.includes(id)) {
  cardStyle = 'matched';
}
```
19b. Both visible and matched cards should show their text. So, add a second condition to the first if statement that checks if the card’s id prop is included in matchedIDs OR it is included in visibleIDs.
Note: In first if statement
```javascript
// 1st if statement
// implement card id array membership check
if (visibleIDs.includes(id) || matchedIDs.includes(id)) {
  cardText = contents;
  click = () => {};
}
```

### Get the Score

Inside Score.js:
20a. Import useSelector from react-redux.
```javascript
import { useSelector } from "react-redux";
```
20b. Import the selectMatchedIDs selector from boardSlice.js.
```javascript
import { selectMatchedIDs } from "../board/boardSlice";
```

Finally, with the selectMatchedIDs()selector, get the number of values in the array of IDs and set the score.

Inside the Score component:

21a. Define a variable named cardsMatched and assign it the data retrieved from calling useSelector() with the selectMatchedIDs selector.
```javascript
const cardsMatched = useSelector(selectMatchedIDs)
```
21b. Replace the 0 inside the returned <div> with the length of cardsMatched.
```javascript
<div className="score-container">Matched: {cardsMatched.length}</div>
```
The game now displays the number of cards you matched!

### Extra Challenge
In Card.js:
22a. Reset the unmatched cards by clicking any card. This involves making a new action handler in Card.js and assigning it to the Card component click in a specific scenario.

Basically, in App.js, copy the tryAgainHandler function and paste it in Card.js's Card component. 
```javascript
  const tryAgainHandler = () => {
    // Add action dispatch below
    dispatch(resetCards())
  };
```
Now, since we are now calling resetCards(), import resetCards at the top of the file.
```javascript
import { selectVisibleIDs, flipCard, selectMatchedIDs, resetCards } from '../../boardSlice';
```
In the third if statement, after 2 flipped cards have taken place, change the click function to the following: 
```javascript
  // 3rd if statement
  // implement number of flipped cards check
  if (visibleIDs.length === 2) {
    click = tryAgainHandler;
  }
```
22b. In index.css there is a ‘no-match’ selector which makes text red and can be applied to the card style when two visible cards are not matching. The challenge here is to apply the style to the text of cards that are flipped over and not matched.
Note: In Card.js, similar to the second if statement, try matchedIDs.includes(id) === false and return cardstyle = 'no-match' as follows: 
```javascript
  if (!matchedIDs.includes(id)) {
    cardStyle = 'no-match';
  }
```
The code now gives a red color style for unmatched cards, but if we want this red color style applied after two cards have been clicked (and keeping the white color style for one card clicked/visible), we can add another parameter to our 4th if statement as follows: 
```javascript
  if (!matchedIDs.includes(id) && visibleIDs.length === 2) {
    cardStyle = 'no-match';
  }
```
```javascript
```

