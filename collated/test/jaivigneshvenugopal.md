# jaivigneshvenugopal
###### \java\seedu\address\logic\commands\AddCommandTest.java
``` java
        @Override
        public ReadOnlyPerson addBlacklistedPerson(ReadOnlyPerson person) {
            fail("This method should not be called.");
            return null;
        }

        @Override
        public ReadOnlyPerson addWhitelistedPerson(ReadOnlyPerson person) {
            fail("This method should not be called.");
            return null;
        }
```
###### \java\seedu\address\logic\commands\AddCommandTest.java
``` java
        @Override
        public String getCurrentListName() {
            fail("This method should not be called.");
            return null;
        }

        @Override
        public void setCurrentListName(String currentList) {
            fail("This method should not be called.");
        }
```
###### \java\seedu\address\logic\commands\AddCommandTest.java
``` java
        @Override
        public ReadOnlyPerson removeBlacklistedPerson(ReadOnlyPerson target) throws PersonNotFoundException {
            fail("This method should not be called.");
            return null;
        }

        @Override
        public ReadOnlyPerson removeWhitelistedPerson(ReadOnlyPerson target) throws PersonNotFoundException {
            fail("This method should not be called.");
            return null;
        }
```
###### \java\seedu\address\logic\commands\AddCommandTest.java
``` java
        @Override
        public ObservableList<ReadOnlyPerson> getFilteredBlacklistedPersonList() {
            fail("This method should not be called.");
            return null;
        }

        @Override
        public ObservableList<ReadOnlyPerson> getFilteredWhitelistedPersonList() {
            return null;
        }
```
###### \java\seedu\address\logic\commands\AddCommandTest.java
``` java
        @Override
        public int updateFilteredBlacklistedPersonList(Predicate<ReadOnlyPerson> predicate) {
            fail("This method should not be called.");
            return 0;
        }

        @Override
        public int updateFilteredWhitelistedPersonList(Predicate<ReadOnlyPerson> predicate) {
            fail("This method should not be called.");
            return 0;
        }
```
###### \java\seedu\address\logic\commands\AddCommandTest.java
``` java
        @Override
        public String getCurrentListName() {
            return "list";
        }
```
###### \java\seedu\address\logic\commands\AddCommandTest.java
``` java
        @Override
        public String getCurrentListName() {
            return "list";
        }
```
###### \java\seedu\address\logic\commands\AddPictureCommandTest.java
``` java
/**
 * Contains integration tests (interaction with the Model) and unit tests for {@code AddPictureCommand}.
 */
public class AddPictureCommandTest extends CommandTest {

    @Test
    public void execute_validIndexUnfilteredListInvalidPath_failure() throws Exception {
        try {
            ReadOnlyPerson personToUpdate = model.getFilteredPersonList().get(INDEX_FIRST_PERSON.getZeroBased());
            AddPictureCommand addPictureCommand = prepareCommand(INDEX_FIRST_PERSON);

            String expectedMessage = String.format(AddPictureCommand.MESSAGE_ADDPIC_FAILURE, personToUpdate.getName());

            assertCommandFailure(addPictureCommand, model, expectedMessage);
            assertTrue(personToUpdate.getAsText().equals(model.getFilteredPersonList()
                    .get(INDEX_FIRST_PERSON.getZeroBased()).getAsText()));
        } catch (CommandException ce) {
            ce.printStackTrace();
        }
    }

    @Test
    public void execute_validIndexUnfilteredListValidPath_success() throws Exception {
        try {
            ReadOnlyPerson personToUpdate = model.getFilteredPersonList().get(INDEX_FIRST_PERSON.getZeroBased());
            AddPictureCommand addPictureCommand = prepareCommand(INDEX_FIRST_PERSON);

            String expectedMessage = ListObserver.MASTERLIST_NAME_DISPLAY_FORMAT
                    + String.format(AddPictureCommand.MESSAGE_ADDPIC_SUCCESS, personToUpdate.getName());

            SetPathCommand setPathCommand = prepareSetPathCommand("src/test/resources/TestProfilePics/");
            setPathCommand.execute();

            ModelManager expectedModel = new ModelManager(model.getAddressBook(), new UserPrefs());
            expectedModel.addProfilePicture(personToUpdate);

            assertCommandSuccess(addPictureCommand, model, expectedMessage, expectedModel);
            assertTrue(personToUpdate.getAsText().equals(model.getFilteredPersonList()
                    .get(INDEX_FIRST_PERSON.getZeroBased()).getAsText()));
        } catch (CommandException ce) {
            ce.printStackTrace();
        }
    }

    @Test
    public void execute_invalidIndexUnfilteredList_throwsCommandException() throws Exception {
        Index outOfBoundIndex = Index.fromOneBased(model.getFilteredPersonList().size() + 1);
        thrown.expect(CommandException.class);
        thrown.expectMessage(Messages.MESSAGE_INVALID_PERSON_DISPLAYED_INDEX);
        prepareCommand(outOfBoundIndex);
        fail(UNEXPECTED_EXECTION);
    }

    @Test
    public void execute_invalidIndexFilteredList_throwsCommandException() throws Exception {
        showFirstPersonOnly(model);

        Index outOfBoundIndex = INDEX_SECOND_PERSON;
        // ensures that outOfBoundIndex is still in bounds of address book list
        assertTrue(outOfBoundIndex.getZeroBased() < model.getAddressBook().getPersonList().size());

        thrown.expect(CommandException.class);
        thrown.expectMessage(Messages.MESSAGE_INVALID_PERSON_DISPLAYED_INDEX);
        prepareCommand(outOfBoundIndex);
        fail(UNEXPECTED_EXECTION);
    }

    @Test
    public void equals() throws Exception {
        AddPictureCommand addPictureFirstCommand = new AddPictureCommand(INDEX_FIRST_PERSON);
        AddPictureCommand addPictureSecondCommand = new AddPictureCommand(INDEX_SECOND_PERSON);

        // same object -> returns true
        assertTrue(addPictureFirstCommand.equals(addPictureFirstCommand));

        // same values -> returns true
        AddPictureCommand addPictureFirstCommandCopy = new AddPictureCommand(INDEX_FIRST_PERSON);
        assertTrue(addPictureFirstCommand.equals(addPictureFirstCommandCopy));

        // different types -> returns false
        assertFalse(addPictureFirstCommand.equals(1));

        // null -> returns false
        assertFalse(addPictureFirstCommand.equals(null));

        // different person -> returns false
        assertFalse(addPictureFirstCommand.equals(addPictureSecondCommand));
    }

    /**
     * Returns a {@code AddPictureCommand} with the parameter {@code index}.
     */
    private AddPictureCommand prepareCommand(Index index) throws CommandException {
        AddPictureCommand addPictureCommand = new AddPictureCommand(index);
        addPictureCommand.setData(model, new CommandHistory(), new UndoRedoStack());
        return addPictureCommand;
    }

    /**
     * Returns a {@code SetPathCommand} with the parameter {@code path}.
     */
    private SetPathCommand prepareSetPathCommand(String path) {
        SetPathCommand setPathCommand = new SetPathCommand(path);
        setPathCommand.setData(model, new CommandHistory(), new UndoRedoStack());
        return setPathCommand;
    }

}
```
###### \java\seedu\address\logic\commands\BanCommandTest.java
``` java
/**
 * Contains integration tests (interaction with the Model) and unit tests for {@code BanCommand}.
 */
public class BanCommandTest extends CommandTest {

    @Test
    public void execute_validIndexUnfilteredList_success() {
        try {
            ReadOnlyPerson personToBan = model.getFilteredPersonList().get(INDEX_FIRST_PERSON.getZeroBased());
            BanCommand banCommand = prepareCommand(INDEX_FIRST_PERSON);

            String expectedMessage = ListObserver.MASTERLIST_NAME_DISPLAY_FORMAT
                    + String.format(BanCommand.MESSAGE_BAN_PERSON_SUCCESS, personToBan.getName());

            ModelManager expectedModel = new ModelManager(model.getAddressBook(), new UserPrefs());
            expectedModel.addBlacklistedPerson(personToBan);

            assertCommandSuccess(banCommand, model, expectedMessage, expectedModel);
            assertTrue(personToBan.getAsText().equals(model.getFilteredPersonList()
                    .get(INDEX_FIRST_PERSON.getZeroBased()).getAsText()));
        } catch (CommandException ce) {
            ce.printStackTrace();
        }
    }

    @Test
    public void execute_banPersonTwice_failure() {
        try {
            ReadOnlyPerson personToBan = model.getFilteredPersonList().get(INDEX_FIRST_PERSON.getZeroBased());

            String expectedMessage = String.format(BanCommand.MESSAGE_BAN_BLACKLISTED_PERSON_FAILURE,
                    personToBan.getName());

            prepareCommand(INDEX_FIRST_PERSON).execute();
            BanCommand banCommand = prepareCommand(INDEX_FIRST_PERSON);

            assertCommandFailure(banCommand, model, expectedMessage);
        } catch (CommandException ce) {
            ce.printStackTrace();
        }
    }

    @Test
    public void execute_invalidIndexUnfilteredList_throwsCommandException() throws Exception {
        Index outOfBoundIndex = Index.fromOneBased(model.getFilteredPersonList().size() + 1);

        thrown.expect(CommandException.class);
        thrown.expectMessage(Messages.MESSAGE_INVALID_PERSON_DISPLAYED_INDEX);
        prepareCommand(outOfBoundIndex);
        fail(UNEXPECTED_EXECTION);
    }

    @Test
    public void execute_invalidIndexFilteredList_throwsCommandException() throws Exception {
        showFirstPersonOnly(model);

        Index outOfBoundIndex = INDEX_SECOND_PERSON;
        // ensures that outOfBoundIndex is still in bounds of address book list
        assertTrue(outOfBoundIndex.getZeroBased() < model.getAddressBook().getPersonList().size());

        thrown.expect(CommandException.class);
        thrown.expectMessage(Messages.MESSAGE_INVALID_PERSON_DISPLAYED_INDEX);
        prepareCommand(outOfBoundIndex);
        fail(UNEXPECTED_EXECTION);
    }

```
###### \java\seedu\address\logic\commands\BanCommandTest.java
``` java
    @Test
    public void equals() throws Exception {
        BanCommand banFirstCommand = new BanCommand(INDEX_FIRST_PERSON);
        BanCommand banSecondCommand = new BanCommand(INDEX_SECOND_PERSON);

        // same object -> returns true
        assertTrue(banFirstCommand.equals(banFirstCommand));

        // same values -> returns true
        BanCommand banFirstCommandCopy = new BanCommand(INDEX_FIRST_PERSON);
        assertTrue(banFirstCommand.equals(banFirstCommandCopy));

        // different types -> returns false
        assertFalse(banFirstCommand.equals(1));

        // null -> returns false
        assertFalse(banFirstCommand.equals(null));

        // different person -> returns false
        assertFalse(banFirstCommand.equals(banSecondCommand));
    }

    /**
     * Returns a {@code BanCommand} with the parameter {@code index}.
     */
    private BanCommand prepareCommand(Index index) throws CommandException {
        BanCommand banCommand = new BanCommand(index);
        banCommand.setData(model, new CommandHistory(), new UndoRedoStack());
        return banCommand;
    }

    /**
     * Returns a {@code BanCommand} with no parameters.
     */
    private BanCommand prepareCommand() throws CommandException {
        BanCommand banCommand = new BanCommand();
        banCommand.setData(model, new CommandHistory(), new UndoRedoStack());
        return banCommand;
    }

}
```
###### \java\seedu\address\logic\commands\BlacklistCommandTest.java
``` java
public class BlacklistCommandTest extends CommandTest {

    private Model expectedModel;
    private BlacklistCommand blacklistCommand;

    @Before
    @Override
    public void setUp() {
        model = new ModelManager(getTypicalAddressBook(), new UserPrefs());
        ListObserver.init(model);
        expectedModel = new ModelManager(model.getAddressBook(), new UserPrefs());

        blacklistCommand = new BlacklistCommand();
        blacklistCommand.setData(model, new CommandHistory(), new UndoRedoStack());
    }

    @Test
    public void execute_listIsNotFiltered_showsSameList() {
        model.setCurrentListName("blacklist");
        assertCommandSuccess(blacklistCommand, model, ListObserver.BLACKLIST_NAME_DISPLAY_FORMAT
                + BlacklistCommand.MESSAGE_SUCCESS, expectedModel);
    }

    @Test
    public void execute_listIsFiltered_showsEverything() {
        model.setCurrentListName("blacklist");
        showFirstBlacklistedPersonOnly(model);
        assertCommandSuccess(blacklistCommand, model, ListObserver.BLACKLIST_NAME_DISPLAY_FORMAT
                + BlacklistCommand.MESSAGE_SUCCESS, expectedModel);
    }
}

```
###### \java\seedu\address\logic\commands\DeletePictureCommandTest.java
``` java
/**
 * Contains integration tests (interaction with the Model) and unit tests for {@code DeletePictureCommand}.
 */
public class DeletePictureCommandTest extends CommandTest {

    @Test
    public void execute_personHasDisplayPicture_success() throws Exception {
        try {
            ReadOnlyPerson personToUpdate = model.getFilteredPersonList().get(INDEX_FIRST_PERSON.getZeroBased());

            SetPathCommand setPathCommand = prepareSetPathCommand("src/test/resources/TestProfilePics/");
            setPathCommand.execute();

            AddPictureCommand addPictureCommand = prepareAddPictureCommand(INDEX_FIRST_PERSON);
            addPictureCommand.execute();

            ModelManager expectedModel = new ModelManager(model.getAddressBook(), new UserPrefs());

            DeletePictureCommand deletePictureCommand = prepareCommand(INDEX_FIRST_PERSON);

            String expectedMessage = ListObserver.MASTERLIST_NAME_DISPLAY_FORMAT
                    + String.format(DeletePictureCommand.MESSAGE_DELETE_PICTURE_SUCCESS, personToUpdate.getName());

            assertCommandSuccess(deletePictureCommand, model, expectedMessage, expectedModel);
            assertTrue(personToUpdate.getAsText().equals(model.getFilteredPersonList()
                    .get(INDEX_FIRST_PERSON.getZeroBased()).getAsText()));
        } catch (CommandException ce) {
            ce.printStackTrace();
        }
    }

    @Test
    public void execute_personHasNoDisplayPicture_failure() throws Exception {
        try {
            ReadOnlyPerson personToUpdate = model.getFilteredPersonList().get(INDEX_FIRST_PERSON.getZeroBased());
            DeletePictureCommand deletePictureCommand = prepareCommand(INDEX_FIRST_PERSON);

            String expectedMessage = String.format(DeletePictureCommand
                    .MESSAGE_DELETE_PICTURE_FAILURE, personToUpdate.getName());

            assertCommandFailure(deletePictureCommand, model, expectedMessage);
            assertTrue(personToUpdate.getAsText().equals(model.getFilteredPersonList()
                    .get(INDEX_FIRST_PERSON.getZeroBased()).getAsText()));
        } catch (CommandException ce) {
            ce.printStackTrace();
        }
    }

    @Test
    public void execute_invalidIndexUnfilteredList_throwsCommandException() throws Exception {
        Index outOfBoundIndex = Index.fromOneBased(model.getFilteredPersonList().size() + 1);
        thrown.expect(CommandException.class);
        thrown.expectMessage(Messages.MESSAGE_INVALID_PERSON_DISPLAYED_INDEX);
        prepareCommand(outOfBoundIndex);
        fail(UNEXPECTED_EXECTION);
    }

    @Test
    public void execute_invalidIndexFilteredList_throwsCommandException() throws Exception {
        showFirstPersonOnly(model);

        Index outOfBoundIndex = INDEX_SECOND_PERSON;
        // ensures that outOfBoundIndex is still in bounds of address book list
        assertTrue(outOfBoundIndex.getZeroBased() < model.getAddressBook().getPersonList().size());

        thrown.expect(CommandException.class);
        thrown.expectMessage(Messages.MESSAGE_INVALID_PERSON_DISPLAYED_INDEX);
        DeletePictureCommand deletePictureCommand = prepareCommand(outOfBoundIndex);
        fail(UNEXPECTED_EXECTION);
    }

    @Test
    public void equals() throws Exception {
        DeletePictureCommand deletePictureFirstCommand = new DeletePictureCommand(INDEX_FIRST_PERSON);
        DeletePictureCommand deletePictureSecondCommand = new DeletePictureCommand(INDEX_SECOND_PERSON);

        // same object -> returns true
        assertTrue(deletePictureFirstCommand.equals(deletePictureFirstCommand));

        // same values -> returns true
        DeletePictureCommand deletePictureFirstCommandCopy = new DeletePictureCommand(INDEX_FIRST_PERSON);
        assertTrue(deletePictureFirstCommandCopy.equals(deletePictureFirstCommandCopy));

        // different types -> returns false
        assertFalse(deletePictureFirstCommand.equals(1));

        // null -> returns false
        assertFalse(deletePictureFirstCommand.equals(null));

        // different person -> returns false
        assertFalse(deletePictureFirstCommand.equals(deletePictureSecondCommand));
    }

    /**
     * Returns a {@code DeletePictureCommand} with the parameter {@code index}.
     */
    private DeletePictureCommand prepareCommand(Index index) throws CommandException {
        DeletePictureCommand deletePictureCommand = new DeletePictureCommand(index);
        deletePictureCommand.setData(model, new CommandHistory(), new UndoRedoStack());
        return deletePictureCommand;
    }

    /**
     * Returns a {@code AddPictureCommand} with the parameter {@code index}.
     */
    private AddPictureCommand prepareAddPictureCommand(Index index) throws CommandException {
        AddPictureCommand addPictureCommand = new AddPictureCommand(index);
        addPictureCommand.setData(model, new CommandHistory(), new UndoRedoStack());
        return addPictureCommand;
    }

    /**
     * Returns a {@code SetPathCommand} with no parameters.
     */
    private SetPathCommand prepareSetPathCommand(String path) {
        SetPathCommand setPathCommand = new SetPathCommand(path);
        setPathCommand.setData(model, new CommandHistory(), new UndoRedoStack());
        return setPathCommand;
    }
}
```
###### \java\seedu\address\logic\commands\RepaidCommandTest.java
``` java
/**
 * Contains integration tests (interaction with the Model) and unit tests for {@code RepaidCommand}.
 */
public class RepaidCommandTest extends CommandTest {

    @Test
    public void execute_validIndexUnfilteredList_success() throws Exception {
        ReadOnlyPerson repaidPerson = model.getFilteredPersonList().get(INDEX_FIRST_PERSON.getZeroBased());
        RepaidCommand repaidCommand = prepareCommand(INDEX_FIRST_PERSON);

        String expectedMessage = ListObserver.MASTERLIST_NAME_DISPLAY_FORMAT
                + String.format(RepaidCommand.MESSAGE_REPAID_PERSON_SUCCESS, repaidPerson.getName());

        ModelManager expectedModel = new ModelManager(model.getAddressBook(), new UserPrefs());
        expectedModel.addWhitelistedPerson(repaidPerson);

        assertCommandSuccess(repaidCommand, model, expectedMessage, expectedModel);
    }

    @Test
    public void execute_repaidPersonTwice_failure() throws Exception {
        ReadOnlyPerson repaidPerson = model.getFilteredPersonList().get(INDEX_FIRST_PERSON.getZeroBased());

        String expectedMessage = String.format(RepaidCommand.MESSAGE_REPAID_PERSON_FAILURE, repaidPerson.getName());

        prepareCommand(INDEX_FIRST_PERSON).execute();
        RepaidCommand repaidCommand = prepareCommand(INDEX_FIRST_PERSON);

        assertCommandFailure(repaidCommand, model, expectedMessage);
    }

    @Test
    public void execute_invalidIndexUnfilteredList_throwsCommandException() throws Exception {
        Index outOfBoundIndex = Index.fromOneBased(model.getFilteredPersonList().size() + 1);
        thrown.expect(CommandException.class);
        thrown.expectMessage(Messages.MESSAGE_INVALID_PERSON_DISPLAYED_INDEX);
        prepareCommand(outOfBoundIndex);
        fail(UNEXPECTED_EXECTION);
    }

    @Test
    public void execute_invalidIndexFilteredList_throwsCommandException() throws Exception {
        showFirstPersonOnly(model);

        Index outOfBoundIndex = INDEX_SECOND_PERSON;
        // ensures that outOfBoundIndex is still in bounds of address book list
        assertTrue(outOfBoundIndex.getZeroBased() < model.getAddressBook().getPersonList().size());

        thrown.expect(CommandException.class);
        thrown.expectMessage(Messages.MESSAGE_INVALID_PERSON_DISPLAYED_INDEX);
        RepaidCommand repaidCommand = prepareCommand(outOfBoundIndex);
        fail(UNEXPECTED_EXECTION);
    }

```
###### \java\seedu\address\logic\commands\RepaidCommandTest.java
``` java
    @Test
    public void equals() {
        try {
            RepaidCommand repaidFirstCommand = new RepaidCommand(INDEX_FIRST_PERSON);
            RepaidCommand repaidSecondCommand = new RepaidCommand(INDEX_SECOND_PERSON);

            // same object -> returns true
            assertTrue(repaidFirstCommand.equals(repaidFirstCommand));

            // same values -> returns true
            RepaidCommand repaidFirstCommandCopy = new RepaidCommand(INDEX_FIRST_PERSON);
            assertTrue(repaidFirstCommand.equals(repaidFirstCommand));

            // different types -> returns false
            assertFalse(repaidFirstCommand.equals(1));

            // null -> returns false
            assertFalse(repaidFirstCommand.equals(null));

            // different person -> returns false
            assertFalse(repaidFirstCommand.equals(repaidSecondCommand));
        } catch (CommandException ce) {
            ce.printStackTrace();
        }
    }

    /**
     * Returns a {@code RepaidCommand} with the parameter {@code index}.
     */
    private RepaidCommand prepareCommand(Index index) throws CommandException {
        RepaidCommand repaidCommand = new RepaidCommand(index);
        repaidCommand.setData(model, new CommandHistory(), new UndoRedoStack());
        return repaidCommand;
    }

    /**
     * Returns a {@code RepaidCommand} with no parameters.
     */
    private RepaidCommand prepareCommand() throws CommandException {
        RepaidCommand repaidCommand = new RepaidCommand();
        repaidCommand.setData(model, new CommandHistory(), new UndoRedoStack());
        return repaidCommand;
    }

}
```
###### \java\seedu\address\logic\commands\SetPathCommandTest.java
``` java
/**
 * Contains integration tests (interaction with the Model) and unit tests for {@code SetPathCommand}.
 */
public class SetPathCommandTest extends CommandTest {

    @Test
    public void execute_setPath_success() throws Exception {
        String path = "src/main/";
        SetPathCommand setPathCommand = prepareSetPathCommand(path);
        setPathCommand.execute();

        assertTrue(ProfilePicturesFolder.getPath().equals(path));
    }

    @Test
    public void execute_setPathMissingForwardSlashGetsConcatenated_success() throws Exception {
        String path = "src/main";
        String expectedPath = "src/main/";

        SetPathCommand setPathCommand = prepareSetPathCommand(path);
        setPathCommand.execute();

        assertTrue(ProfilePicturesFolder.getPath().equals(expectedPath));
    }

    /**
     * Returns a {@code SetPathCommand} with no parameters.
     */
    private SetPathCommand prepareSetPathCommand(String path) {
        SetPathCommand setPathCommand = new SetPathCommand(path);
        setPathCommand.setData(model, new CommandHistory(), new UndoRedoStack());
        return setPathCommand;
    }
}
```
###### \java\seedu\address\logic\commands\UnbanCommandTest.java
``` java
/**
 * Contains integration tests (interaction with the Model) and unit tests for {@code UnbanCommand}.
 */
public class UnbanCommandTest extends CommandTest {

    @Test
    public void execute_validIndexUnfilteredList_success() throws Exception {
        ReadOnlyPerson personToUnban = model.getFilteredBlacklistedPersonList().get(INDEX_FIRST_PERSON.getZeroBased());
        UnbanCommand unbanCommand = prepareCommand(INDEX_FIRST_PERSON);

        String expectedMessage = ListObserver.BLACKLIST_NAME_DISPLAY_FORMAT
                + String.format(UnbanCommand.MESSAGE_UNBAN_PERSON_SUCCESS, personToUnban.getName());

        ModelManager expectedModel = new ModelManager(model.getAddressBook(), new UserPrefs());
        expectedModel.removeBlacklistedPerson(personToUnban);

        assertCommandSuccess(unbanCommand, model, expectedMessage, expectedModel);
    }

    @Test
    public void execute_unbanPersonWhoIsNotBlacklisted_failure() throws Exception {
        ReadOnlyPerson personToUnban = model.getFilteredPersonList().get(INDEX_FIRST_PERSON.getZeroBased());

        String expectedMessage = String.format(UnbanCommand.MESSAGE_UNBAN_PERSON_FAILURE, personToUnban.getName());

        UnbanCommand unbanCommand = prepareCommand(INDEX_FIRST_PERSON);

        assertCommandFailure(unbanCommand, model, expectedMessage);
    }

```
###### \java\seedu\address\logic\commands\UnbanCommandTest.java
``` java
    @Test
    public void equals() {
        try {
            UnbanCommand unbanFirstCommand = new UnbanCommand(INDEX_FIRST_PERSON);
            UnbanCommand unbanSecondCommand = new UnbanCommand(INDEX_SECOND_PERSON);

            // same object -> returns true
            assertTrue(unbanFirstCommand.equals(unbanFirstCommand));

            // same values -> returns true
            UnbanCommand unbanFirstCommandCopy = new UnbanCommand(INDEX_FIRST_PERSON);
            assertTrue(unbanFirstCommand.equals(unbanFirstCommandCopy));

            // different types -> returns false
            assertFalse(unbanFirstCommand.equals(1));

            // null -> returns false
            assertFalse(unbanFirstCommand.equals(null));

            // different person -> returns false
            assertFalse(unbanFirstCommand.equals(unbanSecondCommand));
        } catch (CommandException ce) {
            ce.printStackTrace();
        }
    }

    /**
     * Returns a {@code UnbanCommand} with the parameter {@code index}.
     */
    private UnbanCommand prepareCommand(Index index) throws CommandException {
        UnbanCommand unbanCommand = new UnbanCommand(index);
        unbanCommand.setData(model, new CommandHistory(), new UndoRedoStack());
        return unbanCommand;
    }

    /**
     * Returns a {@code UnbanCommand} with no parameters.
     */
    private UnbanCommand prepareCommand() throws CommandException {
        UnbanCommand unbanCommand = new UnbanCommand();
        unbanCommand.setData(model, new CommandHistory(), new UndoRedoStack());
        return unbanCommand;
    }

}
```
###### \java\seedu\address\logic\commands\WhitelistCommandTest.java
``` java
public class WhitelistCommandTest extends CommandTest {

    private Model expectedModel;
    private WhitelistCommand whitelistCommand;

    @Override
    @Before
    public void setUp() {
        ListObserver.init(model);
        expectedModel = new ModelManager(model.getAddressBook(), new UserPrefs());

        whitelistCommand = new WhitelistCommand();
        whitelistCommand.setData(model, new CommandHistory(), new UndoRedoStack());

        expectedModel.getFilteredWhitelistedPersonList();
    }

    @Test
    public void execute_listIsNotFiltered_showsSameList() {
        model.setCurrentListName("whitelist");
        assertCommandSuccess(whitelistCommand, model,
                ListObserver.WHITELIST_NAME_DISPLAY_FORMAT + whitelistCommand.MESSAGE_SUCCESS, expectedModel);
    }

    @Test
    public void execute_listIsFiltered_showsEverything() {
        showFirstWhitelistedPersonOnly(model);
        assertCommandSuccess(whitelistCommand, model,
                ListObserver.WHITELIST_NAME_DISPLAY_FORMAT + whitelistCommand.MESSAGE_SUCCESS, expectedModel);
    }

}
```
###### \java\seedu\address\logic\parser\AddPictureCommandParserTest.java
``` java
/**
 * As we are only doing white-box testing, our test cases do not cover path variations
 * outside of the AddPictureCommand code. For example, inputs "1" and "1 abc" take the
 * same path through the AddPictureCommand, and therefore we test only one of them.
 * The path variation for those two cases occur inside the ParserUtil, and
 * therefore should be covered by the ParserUtilTest.
 */
public class AddPictureCommandParserTest extends CommandTest {
    private AddPictureCommandParser parser = new AddPictureCommandParser();

    @Test
    public void parse_validArgs_returnsAddPictureCommand() throws Exception {
        assertParseSuccess(parser, "1", new AddPictureCommand(INDEX_FIRST_PERSON));
    }

    @Test
    public void parse_noIndex_returnsAddPictureCommand() throws Exception {
        selectFirstPerson();
        assertEquals(new AddPictureCommand(), new AddPictureCommand(INDEX_FIRST_PERSON));
        assertParseSuccess(parser, "", new AddPictureCommand());
    }

    @Test
    public void parse_invalidArgs_throwsParseException() {
        assertParseFailure(parser, "a", String.format(MESSAGE_INVALID_COMMAND_FORMAT, AddPictureCommand.MESSAGE_USAGE));
    }
}
```
###### \java\seedu\address\logic\parser\BanCommandParserTest.java
``` java
/**
 * As we are only doing white-box testing, our test cases do not cover path variations
 * outside of the BanCommand code. For example, inputs "1" and "1 abc" take the
 * same path through the BanCommand, and therefore we test only one of them.
 * The path variation for those two cases occur inside the ParserUtil, and
 * therefore should be covered by the ParserUtilTest.
 */
public class BanCommandParserTest extends CommandTest {
    private BanCommandParser parser = new BanCommandParser();

    @Test
    public void parse_validArgs_returnsBanCommand() throws Exception {
        assertParseSuccess(parser, "1", new BanCommand(INDEX_FIRST_PERSON));
    }

    @Test
    public void parse_noIndex_returnsBanCommand() throws Exception {
        selectFirstPerson();
        assertEquals(new BanCommand(), new BanCommand(INDEX_FIRST_PERSON));
        assertParseSuccess(parser, "", new BanCommand());
    }

    @Test
    public void parse_invalidArgs_throwsParseException() {
        assertParseFailure(parser, "a", String.format(MESSAGE_INVALID_COMMAND_FORMAT, BanCommand.MESSAGE_USAGE));
    }
}
```
###### \java\seedu\address\logic\parser\DeletePictureCommandParserTest.java
``` java
/**
 * As we are only doing white-box testing, our test cases do not cover path variations
 * outside of the DeletePictureCommand code. For example, inputs "1" and "1 abc" take the
 * same path through the DeletePictureCommand, and therefore we test only one of them.
 * The path variation for those two cases occur inside the ParserUtil, and
 * therefore should be covered by the ParserUtilTest.
 */
public class DeletePictureCommandParserTest extends CommandTest {
    private DeletePictureCommandParser parser = new DeletePictureCommandParser();

    @Test
    public void parse_validArgs_returnsDeletePictureCommand() throws Exception {
        assertParseSuccess(parser, "1", new DeletePictureCommand(INDEX_FIRST_PERSON));
    }

    @Test
    public void parse_noIndex_returnsDeletePictureCommand() throws Exception {
        selectFirstPerson();
        assertEquals(new DeletePictureCommand(), new DeletePictureCommand(INDEX_FIRST_PERSON));
        assertParseSuccess(parser, "", new DeletePictureCommand());
    }

    @Test
    public void parse_invalidArgs_throwsParseException() {
        assertParseFailure(parser, "a", String.format(MESSAGE_INVALID_COMMAND_FORMAT,
                DeletePictureCommand.MESSAGE_USAGE));
    }

}
```
###### \java\seedu\address\logic\parser\RepaidCommandParserTest.java
``` java
/**
 * As we are only doing white-box testing, our test cases do not cover path variations
 * outside of the RepaidCommand code. For example, inputs "1" and "1 abc" take the
 * same path through the RepaidCommand, and therefore we test only one of them.
 * The path variation for those two cases occur inside the ParserUtil, and
 * therefore should be covered by the ParserUtilTest.
 */
public class RepaidCommandParserTest extends CommandTest {

    private RepaidCommandParser parser = new RepaidCommandParser();

    @Test
    public void parse_validArgs_returnsRepaidCommand() throws Exception {
        assertParseSuccess(parser, "1", new RepaidCommand(INDEX_FIRST_PERSON));
    }

    @Test
    public void parse_noIndex_returnsRepaidCommand() throws Exception {
        selectFirstPerson();
        assertEquals(new RepaidCommand(), new RepaidCommand(INDEX_FIRST_PERSON));
        assertParseSuccess(parser, "", new RepaidCommand());
    }

    @Test
    public void parse_invalidArgs_throwsParseException() {
        assertParseFailure(parser, "c", String.format(MESSAGE_INVALID_COMMAND_FORMAT, RepaidCommand.MESSAGE_USAGE));
    }
}
```
###### \java\seedu\address\logic\parser\UnbanCommandParserTest.java
``` java
/**
 * As we are only doing white-box testing, our test cases do not cover path variations
 * outside of the UnbanCommand code. For example, inputs "1" and "1 abc" take the
 * same path through the UnbanCommand, and therefore we test only one of them.
 * The path variation for those two cases occur inside the ParserUtil, and
 * therefore should be covered by the ParserUtilTest.
 */
public class UnbanCommandParserTest extends CommandTest {
    private UnbanCommandParser parser = new UnbanCommandParser();

    @Test
    public void parse_validArgs_returnsUnBanCommand() throws Exception {
        assertParseSuccess(parser, "1", new UnbanCommand(INDEX_FIRST_PERSON));
    }

    @Test
    public void parse_noIndex_returnsUnBanCommand() throws Exception {
        model.changeListTo(BlacklistCommand.COMMAND_WORD);
        selectFirstPerson();
        assertEquals(new UnbanCommand(), new UnbanCommand(INDEX_FIRST_PERSON));
        assertParseSuccess(parser, "", new UnbanCommand());
    }

    @Test
    public void parse_invalidArgs_throwsParseException() {
        assertParseFailure(parser, "a", String.format(MESSAGE_INVALID_COMMAND_FORMAT, UnbanCommand.MESSAGE_USAGE));
    }
}
```
###### \java\seedu\address\logic\WhitelistSyncTest.java
``` java
public class WhitelistSyncTest {

    private static final UndoRedoStack EMPTY_STACK = new UndoRedoStack();
    private static final CommandHistory EMPTY_COMMAND_HISTORY = new CommandHistory();

    private Model model;

    @Before
    public void setUp() {
        model = new ModelManager(getTypicalAddressBook(), new UserPrefs());
        ListObserver.init(model);
    }

    @Test
    public void execute_deleteCommandOnMasterlistDeletesPersonFromWhitelist_success() throws Exception {

        ReadOnlyPerson personToBeDeleted = model.getFilteredWhitelistedPersonList()
                .get(INDEX_FIRST_PERSON.getZeroBased());
        Index index = Index.fromZeroBased(model.getFilteredPersonList().indexOf(personToBeDeleted));

        ModelManager expectedModel = new ModelManager(model.getAddressBook(), new UserPrefs());

        // Ensure person is deleted from masterlist
        expectedModel.deletePerson(personToBeDeleted);
        expectedModel.updateFilteredWhitelistedPersonList(PREDICATE_SHOW_ALL_WHITELISTED_PERSONS);
        expectedModel.setCurrentListName("whitelist");
        expectedModel.changeListTo("whitelist");

        // Preparation done on actual model
        DeleteCommand deleteCommand = prepareDeleteCommand(index);
        deleteCommand.execute();

        String expectedMessage = ListObserver.WHITELIST_NAME_DISPLAY_FORMAT + WhitelistCommand.MESSAGE_SUCCESS;

        // Operation to be done on actual model
        WhitelistCommand whitelistCommand = prepareWhitelistCommand();
        model.setCurrentListName("whitelist");

        assertCommandSuccess(whitelistCommand, model, expectedMessage, expectedModel);
    }

    @Test
    public void execute_banCommandRemovesPersonFromWhitelist_success() throws Exception {

        ReadOnlyPerson toBeBlacklistedPerson = model.getFilteredWhitelistedPersonList()
                .get(INDEX_FIRST_PERSON.getZeroBased());
        Index index = Index.fromZeroBased(model.getFilteredPersonList().indexOf(toBeBlacklistedPerson));

        ModelManager expectedModel = new ModelManager(model.getAddressBook(), new UserPrefs());

        // To make sure person does not exist in whitelist anymore
        toBeBlacklistedPerson = expectedModel.removeWhitelistedPerson(toBeBlacklistedPerson);
        expectedModel.addBlacklistedPerson(toBeBlacklistedPerson);
        expectedModel.setCurrentListName("whitelist");
        expectedModel.changeListTo("whitelist");

        // Preparation done on actual model
        BanCommand banCommand = prepareBanCommand(index);
        banCommand.execute();

        String expectedMessage = ListObserver.WHITELIST_NAME_DISPLAY_FORMAT + WhitelistCommand.MESSAGE_SUCCESS;

        // Operation to be done on actual model
        WhitelistCommand whitelistCommand = prepareWhitelistCommand();
        model.setCurrentListName("whitelist");

        assertCommandSuccess(whitelistCommand, model, expectedMessage, expectedModel);
    }

    @Test
    public void execute_borrowCommandRemovesPersonFromWhitelist_success() throws Exception {

        ReadOnlyPerson borrowedPerson = model.getFilteredWhitelistedPersonList().get(INDEX_FIRST_PERSON.getZeroBased());
        Index index = Index.fromZeroBased(model.getFilteredPersonList().indexOf(borrowedPerson));
        Debt amount = new Debt("500");

        ModelManager expectedModel = new ModelManager(model.getAddressBook(), new UserPrefs());

        // To make sure person does not exist in whitelist anymore
        borrowedPerson = expectedModel.removeWhitelistedPerson(borrowedPerson);
        expectedModel.addDebtToPerson(borrowedPerson, amount);
        expectedModel.setCurrentListName("whitelist");
        expectedModel.changeListTo("whitelist");

        // Preparation done on actual model
        BorrowCommand borrowCommand = prepareBorrowCommand(index, amount);
        borrowCommand.execute();

        String expectedMessage = ListObserver.WHITELIST_NAME_DISPLAY_FORMAT + WhitelistCommand.MESSAGE_SUCCESS;

        // Operation to be done on actual model
        WhitelistCommand whitelistCommand = prepareWhitelistCommand();
        model.setCurrentListName("whitelist");

        assertCommandSuccess(whitelistCommand, model, expectedMessage, expectedModel);
    }

    @Test
    public void execute_paybackCommandRepayExactDebtAddsPersonIntoWhitelist_success() throws Exception {

        ReadOnlyPerson repayingPerson = model.getFilteredPersonList().get(INDEX_FIRST_PERSON.getZeroBased());
        Debt amount = repayingPerson.getDebt();

        Model expectedModel = new ModelManager(new AddressBook(model.getAddressBook()), new UserPrefs());

        // To ensure person exists in whitelist
        repayingPerson = expectedModel.deductDebtFromPerson(repayingPerson, amount);
        expectedModel.addWhitelistedPerson(repayingPerson);
        expectedModel.setCurrentListName("whitelist");
        expectedModel.changeListTo("whitelist");

        // Preparation done on actual model
        PaybackCommand paybackCommand = preparePaybackCommand(INDEX_FIRST_PERSON, amount);
        paybackCommand.execute();

        String expectedMessage = ListObserver.WHITELIST_NAME_DISPLAY_FORMAT + WhitelistCommand.MESSAGE_SUCCESS;

        // Operation to be done on actual model
        WhitelistCommand whitelistCommand = prepareWhitelistCommand();
        model.setCurrentListName("whitelist");

        assertCommandSuccess(whitelistCommand, model, expectedMessage, expectedModel);
    }

    @Test
    public void execute_paybackCommandRepayInexactDebtDoesNotAddPersonIntoWhitelist_success() throws Exception {

        ReadOnlyPerson repayingPerson = model.getFilteredPersonList().get(INDEX_FIRST_PERSON.getZeroBased());
        Debt amount = new Debt(0.5 * (repayingPerson.getDebt().toNumber()));

        Model expectedModel = new ModelManager(new AddressBook(model.getAddressBook()), new UserPrefs());

        // To ensure person only gets his debt decremented. No other actions
        expectedModel.deductDebtFromPerson(repayingPerson, amount);
        expectedModel.setCurrentListName("whitelist");
        expectedModel.changeListTo("whitelist");

        // Preparation done on actual model
        PaybackCommand paybackCommand = preparePaybackCommand(INDEX_FIRST_PERSON, amount);
        paybackCommand.execute();

        String expectedMessage = ListObserver.WHITELIST_NAME_DISPLAY_FORMAT + WhitelistCommand.MESSAGE_SUCCESS;

        // Operation to be done on actual model
        WhitelistCommand whitelistCommand = prepareWhitelistCommand();
        model.setCurrentListName("whitelist");

        assertCommandSuccess(whitelistCommand, model, expectedMessage, expectedModel);
    }

    @Test
    public void execute_paybackCommandRepayExactDebtDoesNotAddBlacklistedPersonIntoWhitelist_success()
            throws Exception {

        ReadOnlyPerson repayingPerson = model.getFilteredBlacklistedPersonList().get(INDEX_FIRST_PERSON.getZeroBased());
        Index index = Index.fromZeroBased(model.getFilteredPersonList().indexOf(repayingPerson));
        Debt amount = repayingPerson.getDebt();

        Model expectedModel = new ModelManager(new AddressBook(model.getAddressBook()), new UserPrefs());
        repayingPerson = expectedModel.deductDebtFromPerson(repayingPerson, amount);

        // To make sure person does not exist in whitelist
        expectedModel.removeWhitelistedPerson(repayingPerson);
        expectedModel.setCurrentListName("whitelist");
        expectedModel.changeListTo("whitelist");

        // Preparation done on actual model
        PaybackCommand paybackCommand = preparePaybackCommand(index, amount);
        paybackCommand.execute();

        String expectedMessage = ListObserver.WHITELIST_NAME_DISPLAY_FORMAT + WhitelistCommand.MESSAGE_SUCCESS;

        // Operation to be done on actual model
        WhitelistCommand whitelistCommand = prepareWhitelistCommand();
        model.setCurrentListName("whitelist");

        assertCommandSuccess(whitelistCommand, model, expectedMessage, expectedModel);
    }

    @Test
    public void execute_editCommandAddDebtRemovesPersonFromWhitelist_success() throws Exception {

        ReadOnlyPerson borrowedPerson = model.getFilteredWhitelistedPersonList().get(INDEX_FIRST_PERSON.getZeroBased());
        Index index = Index.fromZeroBased(model.getFilteredPersonList().indexOf(borrowedPerson));
        Debt amount = new Debt("500");

        Person editedPerson = new PersonBuilder(borrowedPerson).withDebt(amount.toString()).build();

        EditCommand.EditPersonDescriptor descriptor = new EditPersonDescriptorBuilder(editedPerson).build();
        descriptor.setDebt(amount);

        Model expectedModel = new ModelManager(new AddressBook(model.getAddressBook()), new UserPrefs());

        // To ensure person does not exist in whitelist
        expectedModel.updatePerson(borrowedPerson, editedPerson);
        expectedModel.removeWhitelistedPerson(editedPerson);
        expectedModel.setCurrentListName("whitelist");
        expectedModel.changeListTo("whitelist");

        // Preparation done on actual model
        EditCommand editCommand = prepareEditCommand(index, descriptor);
        editCommand.execute();

        String expectedMessage = ListObserver.WHITELIST_NAME_DISPLAY_FORMAT + WhitelistCommand.MESSAGE_SUCCESS;

        // Operation to be done on actual model
        WhitelistCommand whitelistCommand = prepareWhitelistCommand();
        model.setCurrentListName("whitelist");

        assertCommandSuccess(whitelistCommand, model, expectedMessage, expectedModel);
    }

    @Test
    public void execute_editCommandRepayExactDebtAddsPersonIntoWhitelist_success() throws Exception {

        ReadOnlyPerson repayingPerson = model.getFilteredPersonList().get(INDEX_FIRST_PERSON.getZeroBased());
        Debt amount = new Debt("0");

        Person editedPerson = new PersonBuilder(repayingPerson).withDebt(amount.toString()).build();

        EditCommand.EditPersonDescriptor descriptor = new EditPersonDescriptorBuilder(editedPerson).build();
        descriptor.setDebt(amount);

        Model expectedModel = new ModelManager(new AddressBook(model.getAddressBook()), new UserPrefs());

        // To ensure person exists in whitelist
        expectedModel.updatePerson(repayingPerson, editedPerson);
        expectedModel.addWhitelistedPerson(editedPerson);
        expectedModel.setCurrentListName("whitelist");
        expectedModel.changeListTo("whitelist");

        // Preparation done on actual model
        EditCommand editCommand = prepareEditCommand(INDEX_FIRST_PERSON, descriptor);
        editCommand.execute();

        String expectedMessage = ListObserver.WHITELIST_NAME_DISPLAY_FORMAT + WhitelistCommand.MESSAGE_SUCCESS;

        // Operation to be done on actual model
        WhitelistCommand whitelistCommand = prepareWhitelistCommand();
        model.setCurrentListName("whitelist");

        assertCommandSuccess(whitelistCommand, model, expectedMessage, expectedModel);
    }

    @Test
    public void execute_editCommandRepayInexactDebtDoesNotAddPersonIntoWhitelist_success() throws Exception {

        ReadOnlyPerson repayingPerson = model.getFilteredPersonList().get(INDEX_FIRST_PERSON.getZeroBased());
        Debt amount = new Debt(0.5 * (repayingPerson.getDebt().toNumber()));

        Person editedPerson = new PersonBuilder(repayingPerson).withDebt(amount.toString()).build();

        EditCommand.EditPersonDescriptor descriptor = new EditPersonDescriptorBuilder(editedPerson).build();
        descriptor.setDebt(amount);

        Model expectedModel = new ModelManager(new AddressBook(model.getAddressBook()), new UserPrefs());
        expectedModel.updatePerson(repayingPerson, editedPerson);
        expectedModel.setCurrentListName("whitelist");
        expectedModel.changeListTo("whitelist");

        // Preparation done on actual model
        EditCommand editCommand = prepareEditCommand(INDEX_FIRST_PERSON, descriptor);
        editCommand.execute();

        String expectedMessage = ListObserver.WHITELIST_NAME_DISPLAY_FORMAT + WhitelistCommand.MESSAGE_SUCCESS;

        // Operation to be done on actual model
        WhitelistCommand whitelistCommand = prepareWhitelistCommand();
        model.setCurrentListName("whitelist");

        assertCommandSuccess(whitelistCommand, model, expectedMessage, expectedModel);
    }

    @Test
    public void execute_editCommandRemoveExactDebtDoesNotAddBlacklistedPersonIntoWhitelist_success()
            throws Exception {

        ReadOnlyPerson repayingPerson = model.getFilteredBlacklistedPersonList().get(INDEX_FIRST_PERSON.getZeroBased());
        Index index = Index.fromZeroBased(model.getFilteredPersonList().indexOf(repayingPerson));
        Debt amount = new Debt("0");

        Person editedPerson = new PersonBuilder(repayingPerson).withDebt(amount.toString()).build();
        editedPerson.setIsWhitelisted(false);

        EditCommand.EditPersonDescriptor descriptor = new EditPersonDescriptorBuilder(editedPerson).build();
        descriptor.setDebt(amount);

        Model expectedModel = new ModelManager(new AddressBook(model.getAddressBook()), new UserPrefs());
        expectedModel.updatePerson(repayingPerson, editedPerson);
        expectedModel.setCurrentListName("whitelist");
        expectedModel.changeListTo("whitelist");

        // Preparation done on actual model
        EditCommand editCommand = prepareEditCommand(index, descriptor);
        editCommand.execute();

        String expectedMessage = ListObserver.WHITELIST_NAME_DISPLAY_FORMAT + WhitelistCommand.MESSAGE_SUCCESS;

        // Operation to be done on actual model
        WhitelistCommand whitelistCommand = prepareWhitelistCommand();
        model.setCurrentListName("whitelist");

        assertCommandSuccess(whitelistCommand, model, expectedMessage, expectedModel);
    }


    @Test
    public void execute_unbanCommandAddsUnBlacklistedPersonIntoWhitelistIfDebtCleared_success()
            throws Exception {

        ReadOnlyPerson unbannedPerson = model.getFilteredBlacklistedPersonList().get(INDEX_FIRST_PERSON.getZeroBased());
        Index index = Index.fromZeroBased(model.getFilteredPersonList().indexOf(unbannedPerson));

        Model expectedModel = new ModelManager(new AddressBook(model.getAddressBook()), new UserPrefs());

        // Preparation done on actual model
        RepaidCommand repaidCommand = prepareRepaidCommand(index);
        repaidCommand.execute();

        model.updateFilteredBlacklistedPersonList(PREDICATE_SHOW_ALL_BLACKLISTED_PERSONS);

        // addWhitelistedPerson() method will set debt to zero and
        // Generates new date repaid.
        // Does not add person into whitelist as he is blacklisted
        unbannedPerson = expectedModel.addWhitelistedPerson(unbannedPerson);

        // removeBlacklistedPerson() now unbans the person
        unbannedPerson = expectedModel.removeBlacklistedPerson(unbannedPerson);

        // Person will be added to whitelisted as he is now unbanned
        expectedModel.addWhitelistedPerson(unbannedPerson);
        expectedModel.setCurrentListName("whitelist");
        expectedModel.changeListTo("whitelist");

        // Preparation done on actual model
        UnbanCommand unbanCommand = prepareUnbanCommand(index);
        unbanCommand.execute();

        String expectedMessage = ListObserver.WHITELIST_NAME_DISPLAY_FORMAT + WhitelistCommand.MESSAGE_SUCCESS;

        // Operation to be done on actual model
        WhitelistCommand whitelistCommand = prepareWhitelistCommand();
        model.setCurrentListName("whitelist");

        assertCommandSuccess(whitelistCommand, model, expectedMessage, expectedModel);
    }

    @Test
    public void execute_undoCommandRemovesWhitelistedPerson_success()
            throws Exception {

        Model expectedModel = new ModelManager(new AddressBook(model.getAddressBook()), new UserPrefs());
        expectedModel.setCurrentListName("whitelist");
        expectedModel.changeListTo("whitelist");

        // Preparation done on actual model
        RepaidCommand repaidCommand = new RepaidCommand(INDEX_FIRST_PERSON);
        repaidCommand.setData(model, EMPTY_COMMAND_HISTORY, EMPTY_STACK);

        // Preparation done on actual model
        UndoRedoStack undoRedoStack = prepareStack(
                Arrays.asList(repaidCommand), Collections.emptyList());
        UndoCommand undoCommand = new UndoCommand();
        undoCommand.setData(model, EMPTY_COMMAND_HISTORY, undoRedoStack);

        // Preparation done on actual model
        repaidCommand.execute();

        // Preparation done on actual model
        undoCommand.execute();

        String expectedMessage = ListObserver.WHITELIST_NAME_DISPLAY_FORMAT + WhitelistCommand.MESSAGE_SUCCESS;

        // Operation to be done on actual model
        WhitelistCommand whitelistCommand = prepareWhitelistCommand();
        model.setCurrentListName("whitelist");

        assertCommandSuccess(whitelistCommand, model, expectedMessage, expectedModel);
    }




    /**
     * Returns a {@code BorrowCommand} with the parameter {@code index} & {@code amount}.
     */
    private BorrowCommand prepareBorrowCommand(Index index, Debt amount) throws CommandException {
        BorrowCommand borrowCommand = new BorrowCommand(index, amount);
        borrowCommand.setData(model, new CommandHistory(), new UndoRedoStack());
        return borrowCommand;
    }

    /**
     * Returns a {@code EditCommand} with the parameter {@code index} & {@code EditPersonDescriptor}.
     */
    private EditCommand prepareEditCommand(Index index, EditCommand.EditPersonDescriptor descriptor) throws
            CommandException {
        EditCommand editCommand = new EditCommand(index, descriptor);
        editCommand.setData(model, new CommandHistory(), new UndoRedoStack());
        return editCommand;
    }

    /**
     * Returns a {@code WhitelistCommand}.
     */
    private WhitelistCommand prepareWhitelistCommand() {
        WhitelistCommand whitelistCommand = new WhitelistCommand();
        whitelistCommand.setData(model, new CommandHistory(), new UndoRedoStack());
        return whitelistCommand;
    }

    /**
     * Returns a {@code RepaidCommand} with the parameter {@code index}.
     */
    private RepaidCommand prepareRepaidCommand(Index index) throws CommandException {
        RepaidCommand repaidCommand = new RepaidCommand(index);
        repaidCommand.setData(model, new CommandHistory(), new UndoRedoStack());
        return repaidCommand;
    }

    /**
     * Returns a {@code UnbanCommand} with the parameter {@code index}.
     */
    private UnbanCommand prepareUnbanCommand(Index index) throws CommandException {
        UnbanCommand unbanCommand = new UnbanCommand(index);
        unbanCommand.setData(model, new CommandHistory(), new UndoRedoStack());
        return unbanCommand;
    }

    /**
     * Returns a {@code PaybackCommand} with the parameter {@code index} & {@code amount}.
     */
    private PaybackCommand preparePaybackCommand(Index index, Debt amount) throws CommandException {
        PaybackCommand paybackCommand = new PaybackCommand(index, amount);
        paybackCommand.setData(model, new CommandHistory(), new UndoRedoStack());
        return paybackCommand;
    }

    /**
     * Returns a {@code DeleteCommand} with the parameter {@code index}.
     */
    private DeleteCommand prepareDeleteCommand(Index index) throws CommandException {
        DeleteCommand deleteCommand = new DeleteCommand(index);
        deleteCommand.setData(model, new CommandHistory(), new UndoRedoStack());
        return deleteCommand;
    }

    /**
     * Returns a {@code BanCommand} with the parameter {@code index}.
     */
    private BanCommand prepareBanCommand(Index index) throws CommandException {
        BanCommand banCommand = new BanCommand(index);
        banCommand.setData(model, new CommandHistory(), new UndoRedoStack());
        return banCommand;
    }
}
```
###### \java\seedu\address\testutil\TypicalPersons.java
``` java
    // Blacklisted persons.
    public static final ReadOnlyPerson JELENA = new PersonBuilder().withName("Jelena Neo")
            .withAddress("123, Jurong West Ave 6, #08-111").withDebt("1234567").withTotalDebt("1234567")
            .withEmail("alice@example.com").withDeadline(Deadline.NO_DEADLINE_SET).withHandphone("85355255")
            .withInterest(Interest.NO_INTEREST_SET).withHomePhone("61234123")
            .withOfficePhone(OfficePhone.NO_OFFICE_PHONE_SET).withTags("friends").withPostalCode("683123").build();
    public static final ReadOnlyPerson WEIPING = new PersonBuilder().withName("Khoo Wei Ping")
            .withAddress("311, Clementi Ave 2, #02-25").withPostalCode("111111").withDebt("1234567")
            .withTotalDebt("1234567").withEmail("johnd@example.com").withInterest(Interest.NO_INTEREST_SET)
            .withHomePhone("65600222").withHandphone("98765432").withTags("friends")
            .withOfficePhone(OfficePhone.NO_OFFICE_PHONE_SET).build();
    public static final ReadOnlyPerson JAIVIGNESH = new PersonBuilder().withName("Jaivignesh Venugopal")
            .withDeadline(Deadline.NO_DEADLINE_SET).withHandphone("95352563").withEmail("heinz@example.com")
            .withAddress("wall street").withPostalCode("111111").withInterest(Interest.NO_INTEREST_SET)
            .withHomePhone("63433233").withOfficePhone(OfficePhone.NO_OFFICE_PHONE_SET).withDebt("1234567")
            .withTotalDebt("1234567").build();
    public static final ReadOnlyPerson LAWRENCE = new PersonBuilder().withName("Lawrence Koh")
            .withDeadline(Deadline.NO_DEADLINE_SET).withHandphone("87652533").withInterest(Interest.NO_INTEREST_SET)
            .withEmail("cornelia@example.com").withAddress("10th street").withHomePhone("64748494")
            .withOfficePhone(OfficePhone.NO_OFFICE_PHONE_SET).withPostalCode("111111").withDebt("1234567")
            .withTotalDebt("1234567").build();

    // Whitelisted persons. Debt has to be zero!
    public static final ReadOnlyPerson ARCHANA = new PersonBuilder().withName("Archana Pradeep")
            .withDeadline(Deadline.NO_DEADLINE_SET).withHandphone("94812224").withInterest(Interest.NO_INTEREST_SET)
            .withEmail("werner@example.com").withAddress("michegan ave").withOfficePhone("60000010")
            .withHomePhone("61111101").withPostalCode("111111").withDebt("0").withTotalDebt("123456").build();
    public static final ReadOnlyPerson SIRISHA = new PersonBuilder().withName("Lakshmi Sirisha")
            .withDeadline(Deadline.NO_DEADLINE_SET).withHandphone("94822427").withInterest(Interest.NO_INTEREST_SET)
            .withEmail("lydia@example.com").withAddress("little tokyo").withHomePhone("62222202")
            .withOfficePhone("60000020").withPostalCode("111111").withDebt("0").withTotalDebt("123456").build();
    public static final ReadOnlyPerson RUSHAN = new PersonBuilder().withName("Khor Ru Shan")
            .withDeadline(Deadline.NO_DEADLINE_SET).withHandphone("94823442").withInterest(Interest.NO_INTEREST_SET)
            .withEmail("anna@example.com").withAddress("4th street").withHomePhone("63333303")
            .withPostalCode("111111").withDebt("0").withTotalDebt("123456").withOfficePhone("60000030").build();

```
###### \java\seedu\address\testutil\TypicalPersons.java
``` java
    /**
     * Returns an {@code AddressBook} with all the typical persons.
     */
    public static AddressBook getTypicalAddressBook() {
        AddressBook ab = new AddressBook();
        for (ReadOnlyPerson person : getTypicalPersons()) {
            try {
                ab.addPerson(person);
            } catch (DuplicatePersonException e) {
                assert false : "It is not possible to have a duplicate person added into the addressbook";
            }
        }

        for (ReadOnlyPerson person : getTypicalBlacklistedPersons()) {
            ab.addBlacklistedPerson(person);
        }

        for (ReadOnlyPerson person : getTypicalWhitelistedPersons()) {
            ab.addWhitelistedPerson(person);
        }

        for (ReadOnlyPerson person : getTypicalOverdueListPersons()) {
            ab.addOverdueDebtPerson(person);
        }
        return ab;
    }

    public static List<ReadOnlyPerson> getTypicalPersons() {
        return new ArrayList<>(Arrays
                .asList(ALICE, BENSON, CARL, DANIEL, ELLE, FIONA, GEORGE,
                        JELENA, WEIPING, JAIVIGNESH, LAWRENCE, ARCHANA, SIRISHA, RUSHAN, KENNARD));
    }

```
###### \java\seedu\address\testutil\TypicalPersons.java
``` java
    public static List<ReadOnlyPerson> getTypicalBlacklistedPersons() {
        return new ArrayList<>(Arrays.asList(JELENA, WEIPING, JAIVIGNESH, LAWRENCE));
    }

    public static List<ReadOnlyPerson> getTypicalWhitelistedPersons() {
        return new ArrayList<>(Arrays.asList(ARCHANA, SIRISHA, RUSHAN));
    }

```
