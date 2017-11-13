# jaivigneshvenugopal
###### \java\seedu\address\commons\core\ListObserver.java
``` java
/**
 * Monitors current displayed list on person list panel.
 * Obtains or updates the various lists according to the changes made to the current displayed list.
 */
public class ListObserver {

    public static final String MASTERLIST_NAME_DISPLAY_FORMAT = "MASTERLIST:\n";
    public static final String BLACKLIST_NAME_DISPLAY_FORMAT = "BLACKLIST:\n";
    public static final String WHITELIST_NAME_DISPLAY_FORMAT = "WHITELIST:\n";
    public static final String OVERDUELIST_NAME_DISPLAY_FORMAT = "OVERDUELIST:\n";

    private static Model model;

    public static void init(Model newModel) {
        model = newModel;
    }

    /**
     * Monitors current displayed list on person list panel.
     * @return updated version of the current displayed list.
     */
    public static ObservableList<ReadOnlyPerson> getCurrentFilteredList() {
        String currentList = model.getCurrentListName();

        switch (currentList) {

        case BlacklistCommand.COMMAND_WORD:
            return model.getFilteredBlacklistedPersonList();

        case WhitelistCommand.COMMAND_WORD:
            return model.getFilteredWhitelistedPersonList();

        case OverdueListCommand.COMMAND_WORD:
            return model.getFilteredOverduePersonList();

        default:
            return model.getFilteredPersonList();
        }
    }

    /**
     * Monitors current displayed list on person list panel.
     * @param predicate is used to update the current displayed list.
     * @return updated version of the current displayed list.
     */
    public static int updateCurrentFilteredList(Predicate<ReadOnlyPerson> predicate) {
        String currentList = model.getCurrentListName();

        switch (currentList) {

        case BlacklistCommand.COMMAND_WORD:
            model.changeListTo(BlacklistCommand.COMMAND_WORD);
            return model.updateFilteredBlacklistedPersonList(predicate);

        case WhitelistCommand.COMMAND_WORD:
            model.changeListTo(WhitelistCommand.COMMAND_WORD);
            return model.updateFilteredWhitelistedPersonList(predicate);

        case OverdueListCommand.COMMAND_WORD:
            model.changeListTo(OverdueListCommand.COMMAND_WORD);
            return model.updateFilteredOverduePersonList(predicate);

        default:
            return model.updateFilteredPersonList(predicate);
        }
    }

    /**
     * Monitors current displayed list on person list panel.
     * @return name of current displayed list.
     */
    public static String getCurrentListName() {
        String currentList = model.getCurrentListName();

        switch (currentList) {

        case BlacklistCommand.COMMAND_WORD:
            return BLACKLIST_NAME_DISPLAY_FORMAT;

        case WhitelistCommand.COMMAND_WORD:
            return WHITELIST_NAME_DISPLAY_FORMAT;

        case OverdueListCommand.COMMAND_WORD:
            return OVERDUELIST_NAME_DISPLAY_FORMAT;

        default:
            return MASTERLIST_NAME_DISPLAY_FORMAT;
        }
    }

    /**
     * Monitors current displayed list on person list panel.
     * @return {@code Index} of current selected person.
     */
    public static Index getIndexOfSelectedPersonInCurrentList() {
        if (model.getSelectedPerson() == null) {
            return null;
        }

        Index index;
        switch (model.getCurrentListName()) {

        case "blacklist":
            index = Index.fromZeroBased(model.getFilteredBlacklistedPersonList().indexOf(model.getSelectedPerson()));
            break;
        case "whitelist":
            index = Index.fromZeroBased(model.getFilteredWhitelistedPersonList().indexOf(model.getSelectedPerson()));
            break;
        case "overduelist":
            index = Index.fromZeroBased(model.getFilteredOverduePersonList().indexOf(model.getSelectedPerson()));
            break;
        default:
            index = Index.fromZeroBased(model.getFilteredPersonList().indexOf(model.getSelectedPerson()));
        }

        return index;
    }

    /**
     * Returns the {@code Index} of person in current displayed list.
     */
    public static Index getIndexOfPersonInCurrentList(ReadOnlyPerson person) {
        Index index;

        switch (model.getCurrentListName()) {
        case "blacklist":
            if (model.getFilteredBlacklistedPersonList().contains(person)) {
                index = Index.fromZeroBased(model.getFilteredBlacklistedPersonList()
                        .indexOf(person));
            } else {
                index = null;
            }
            break;

        case "whitelist":
            if (model.getFilteredWhitelistedPersonList().contains(person)) {
                index = Index.fromZeroBased(model.getFilteredWhitelistedPersonList()
                        .indexOf(person));
            } else {
                index = null;
            }
            break;

        case "overduelist":
            if (model.getFilteredOverduePersonList().contains(person)) {
                index = Index.fromZeroBased(model.getFilteredOverduePersonList()
                        .indexOf(person));
            } else {
                index = null;
            }
            break;

        default:
            if (model.getFilteredPersonList().contains(person)) {
                index = Index.fromZeroBased(model.getFilteredPersonList().indexOf(person));
            } else {
                index = null;
            }
        }

        return index;
    }

    /**
     * Returns the currently selected person.
     */
    public static ReadOnlyPerson getSelectedPerson() {
        return model.getSelectedPerson();
    }
}
```
###### \java\seedu\address\commons\core\ProfilePicturesFolder.java
``` java
/**
 * Contains path to user's folder that contains profile pictures of all clients.
 */
public class ProfilePicturesFolder {

    private static String profilePicsFolderPath = "/images/profilePics/";

    public static void setPath(String path) {
        profilePicsFolderPath = path;
    }

    public static String getPath() {
        return profilePicsFolderPath;
    }
}
```
###### \java\seedu\address\commons\events\ui\ChangeInternalListEvent.java
``` java
/**
 * Represents a change of internal list in the Person List Panel
 */
public class ChangeInternalListEvent extends BaseEvent {

    private final String listName;

    public ChangeInternalListEvent(String listName) {
        this.listName = listName;
    }

    public String getListName() {
        return listName;
    }

    @Override
    public String toString() {
        return this.getClass().getSimpleName();
    }
}
```
###### \java\seedu\address\commons\events\ui\MissingDisplayPictureEvent.java
``` java
/**
 * Represents an event where person has a missing display picture when he is supposed to have one.
 */
public class MissingDisplayPictureEvent extends BaseEvent {

    private final ReadOnlyPerson person;

    public MissingDisplayPictureEvent(ReadOnlyPerson person) {
        this.person = person;
    }

    public ReadOnlyPerson getPerson() {
        return person;
    }

    @Override
    public String toString() {
        return this.getClass().getSimpleName();
    }
}
```
###### \java\seedu\address\logic\commands\AddPictureCommand.java
``` java
/**
 * Adds a display picture to a person.
 */
public class AddPictureCommand extends UndoableCommand {

    public static final String COMMAND_WORD = "addpic";
    public static final String MESSAGE_USAGE = COMMAND_WORD
            + ": Updates the profile picture of the currently selected person or the person"
            + "identified by the index number used in the last "
            + "person listing.\n"
            + "Parameters: INDEX (optional, must be a positive integer if present)\n"
            + "Example: " + COMMAND_WORD + " 1";
    public static final String MESSAGE_ADDPIC_SUCCESS = "%1$s profile picture has been updated!";
    public static final String MESSAGE_ADDPIC_FAILURE = "Unable to update profile picture!";

    private final ReadOnlyPerson personToUpdate;

    public AddPictureCommand() throws CommandException {
        personToUpdate = selectPersonForCommand();
    }

    public AddPictureCommand(Index targetIndex) throws CommandException {
        personToUpdate = selectPersonForCommand(targetIndex);
    }

    @Override
    public CommandResult executeUndoableCommand() throws CommandException {
        ReadOnlyPerson targetPerson;
        try {
            targetPerson = model.addProfilePicture(personToUpdate);
        } catch (ProfilePictureNotFoundException ppnfe) {
            throw new CommandException(String.format(MESSAGE_ADDPIC_FAILURE, personToUpdate.getName()));
        }

        ListObserver.updateCurrentFilteredList(PREDICATE_SHOW_ALL_PERSONS);
        reselectPerson(targetPerson);

        String currentList = ListObserver.getCurrentListName();

        return new CommandResult(currentList + String.format(MESSAGE_ADDPIC_SUCCESS, personToUpdate.getName()));
    }

    @Override
    public boolean equals(Object other) {
        return other == this // short circuit if same object
                || (other instanceof AddPictureCommand // instanceof handles nulls
                && this.personToUpdate.equals(((AddPictureCommand) other).personToUpdate)); // state check
    }
}
```
###### \java\seedu\address\logic\commands\BanCommand.java
``` java
/**
 * Adds a person identified using it's last displayed index into the blacklist.
 */
public class BanCommand extends UndoableCommand {

    public static final String COMMAND_WORD = "ban";

    public static final String MESSAGE_USAGE = COMMAND_WORD
            + ": Bans the currently selected person or the person identified by the index number used in the last"
            + " person listing.\n"
            + "Parameters: INDEX (optional, must be a positive integer if present)\n"
            + "Example: " + COMMAND_WORD + " 1";
    public static final String MESSAGE_BAN_PERSON_SUCCESS = "%1$s has been added to BLACKLIST";
    public static final String MESSAGE_BAN_BLACKLISTED_PERSON_FAILURE = "%1$s is already in BLACKLIST!";

    private final ReadOnlyPerson personToBan;

    public BanCommand() throws CommandException {
        personToBan = selectPersonForCommand();
    }

    public BanCommand(Index targetIndex) throws CommandException {
        personToBan = selectPersonForCommand(targetIndex);
    }

    @Override
    public CommandResult executeUndoableCommand() throws CommandException {
        if (personToBan.isBlacklisted()) {
            throw new CommandException(String.format(MESSAGE_BAN_BLACKLISTED_PERSON_FAILURE, personToBan.getName()));
        }

        ReadOnlyPerson targetPerson = model.addBlacklistedPerson(personToBan);

        ListObserver.updateCurrentFilteredList(PREDICATE_SHOW_ALL_PERSONS);
        reselectPerson(targetPerson);

        String currentList = ListObserver.getCurrentListName();

        return new CommandResult(currentList + String.format(MESSAGE_BAN_PERSON_SUCCESS, personToBan.getName()));
    }

    @Override
    public boolean equals(Object other) {
        return other == this // short circuit if same object
                || (other instanceof BanCommand // instanceof handles nulls
                && this.personToBan.equals(((BanCommand) other).personToBan)); // state check
    }
}
```
###### \java\seedu\address\logic\commands\BlacklistCommand.java
``` java
/**
 * Lists all blacklisted persons in the address book to the user.
 */
public class BlacklistCommand extends Command {

    public static final String COMMAND_WORD = "blacklist";
    public static final String COMMAND_WORD_ALIAS = "bl";

    public static final String MESSAGE_SUCCESS = "Listed all debtors "
            + "who are prohibited from borrowing money";


    @Override
    public CommandResult execute() {
        requireNonNull(model);
        model.deselectPerson();
        model.changeListTo(COMMAND_WORD);
        model.updateFilteredBlacklistedPersonList(PREDICATE_SHOW_ALL_BLACKLISTED_PERSONS);
        String currentList = ListObserver.getCurrentListName();
        return new CommandResult(currentList + MESSAGE_SUCCESS);
    }
}
```
###### \java\seedu\address\logic\commands\DeletePictureCommand.java
``` java
/**
 * Removes the display picture of a person.
 */
public class DeletePictureCommand extends UndoableCommand {

    public static final String COMMAND_WORD = "delpic";
    public static final String MESSAGE_USAGE = COMMAND_WORD
            + ": Removes the profile picture of the currently selected person or the person"
            + "identified by the index number used in the last "
            + "person listing.\n"
            + "Parameters: INDEX (optional, must be a positive integer if present)\n"
            + "Example: " + COMMAND_WORD + " 1";
    public static final String MESSAGE_DELETE_PICTURE_SUCCESS = "%1$s profile picture has been removed!";
    public static final String MESSAGE_DELETE_PICTURE_FAILURE = "%1$s does not have a profile picture!";

    private final ReadOnlyPerson personToUpdate;

    public DeletePictureCommand() throws CommandException {
        personToUpdate = selectPersonForCommand();
    }

    public DeletePictureCommand(Index targetIndex) throws CommandException {
        personToUpdate = selectPersonForCommand(targetIndex);
    }

    @Override
    public CommandResult executeUndoableCommand() throws CommandException {
        ReadOnlyPerson targetPerson;
        try {
            targetPerson = model.removeProfilePicture(personToUpdate);
        } catch (ProfilePictureNotFoundException e) {
            throw new CommandException(String.format(MESSAGE_DELETE_PICTURE_FAILURE, personToUpdate.getName()));
        }

        ListObserver.updateCurrentFilteredList(PREDICATE_SHOW_ALL_PERSONS);
        reselectPerson(targetPerson);

        String currentList = ListObserver.getCurrentListName();

        return new CommandResult(currentList + String.format(MESSAGE_DELETE_PICTURE_SUCCESS, personToUpdate.getName()));
    }

    @Override
    public boolean equals(Object other) {
        return other == this // short circuit if same object
                || (other instanceof DeletePictureCommand // instanceof handles nulls
                && this.personToUpdate.equals(((DeletePictureCommand) other).personToUpdate));
    }

}
```
###### \java\seedu\address\logic\commands\EditCommand.java
``` java
    /**
     * Does the appropriate list status checks on {@code personToEdit}
     * and changes the list status of {@code editedPerson} accordingly
     * @return editedPerson
     */
    private Person listSyncChecks(ReadOnlyPerson personToEdit, Person editedPerson) {
        if (personToEdit.isWhitelisted() && editedPerson.getDebt().toNumber() > 0) {
            editedPerson.setIsWhitelisted(false);
            editedPerson.setDateRepaid(new DateRepaid());
        } else if (!personToEdit.isWhitelisted() && !personToEdit.isBlacklisted()
                && editedPerson.getDebt().toNumber() == 0) {
            editedPerson.setIsWhitelisted(true);
            editedPerson.setDateRepaid(new DateRepaid(formatDate(new Date())));
        }
        if (editedPerson.getDebt().toNumber() == 0) {
            try {
                editedPerson.setDeadline(new Deadline(Deadline.NO_DEADLINE_SET));
                editedPerson.setHasOverdueDebt(false);
            } catch (IllegalValueException ive) {
                assert false : "Cannot happen as input for new deadline was controlled.";
            }
        }
        if (!editedPerson.getDeadline().value.equals(Deadline.NO_DEADLINE_SET)) {
            Date editedPersonDeadline = DateUtil.convertStringToDate(editedPerson.getDeadline().valueToDisplay);
            Date currentDate = new Date();
            if (personToEdit.hasOverdueDebt() && currentDate.before(editedPersonDeadline)) {
                editedPerson.setHasOverdueDebt(false);
            }
            if (!personToEdit.hasOverdueDebt() && editedPersonDeadline.before(currentDate)) {
                editedPerson.setHasOverdueDebt(true);
            }
        }
        return editedPerson;
    }

```
###### \java\seedu\address\logic\commands\RepaidCommand.java
``` java
/**
 * Adds a person identified using it's last displayed index into the whitelist.
 * Resets person's debt to zero and sets the date repaid field.
 */
public class RepaidCommand extends UndoableCommand {

    public static final String COMMAND_WORD = "repaid";
    public static final String COMMAND_WORD_ALIAS = "rp";

    public static final String MESSAGE_USAGE = COMMAND_WORD
            + ": Adds the currently selected person or the person identified by the index number into the whitelist"
            + " and concurrently clear his/her debt.\n"
            + "Parameters: INDEX (optional, must be a positive integer if present)\n"
            + "Example: " + COMMAND_WORD + " 1";
    public static final String MESSAGE_REPAID_PERSON_SUCCESS = "%1$s has now repaid his/her debt";
    public static final String MESSAGE_REPAID_PERSON_FAILURE = "%1$s has already repaid debt!";

    private final ReadOnlyPerson repaidDebtor;

    public RepaidCommand() throws CommandException {
        repaidDebtor = selectPersonForCommand();
    }

    public RepaidCommand(Index targetIndex) throws CommandException {
        repaidDebtor = selectPersonForCommand(targetIndex);
    }

    @Override
    public CommandResult executeUndoableCommand() throws CommandException {
        if (repaidDebtor.getDebt().toNumber() == 0) {
            throw new CommandException(String.format(MESSAGE_REPAID_PERSON_FAILURE, repaidDebtor.getName()));
        }

        ReadOnlyPerson targetPerson = model.addWhitelistedPerson(repaidDebtor);
        try {
            targetPerson = model.removeOverdueDebtPerson(targetPerson);
            targetPerson = model.resetDeadlineForPerson(targetPerson);
        } catch (PersonNotFoundException pnfe) {
            assert false : "The target person cannot be missing";
        }
        ListObserver.updateCurrentFilteredList(PREDICATE_SHOW_ALL_PERSONS);
        reselectPerson(targetPerson);

        String currentList = ListObserver.getCurrentListName();

        return new CommandResult(currentList + String.format(MESSAGE_REPAID_PERSON_SUCCESS, targetPerson.getName()));
    }

    @Override
    public boolean equals(Object other) {
        return other == this // short circuit if same object
                || (other instanceof RepaidCommand // instanceof handles nulls
                && this.repaidDebtor.equals(((RepaidCommand) other).repaidDebtor)); // state check
    }
}
```
###### \java\seedu\address\logic\commands\SetPathCommand.java
``` java
/**
 * Sets the absolute path to the profile pictures folder that is residing in user's workspace.
 */
public class SetPathCommand extends UndoableCommand {

    public static final String COMMAND_WORD = "setpath";
    public static final String MESSAGE_SUCCESS = "Location to access profile pictures is now set!";
    public static final String MESSAGE_FAILURE = "Path is invalid!";
    public static final String MESSAGE_USAGE = COMMAND_WORD + "C:/Users/acer/Desktop/SE/profilepic/";

    private String path;

    public SetPathCommand(String path) {
        this.path = path;
    }

    @Override
    public CommandResult executeUndoableCommand() throws CommandException {
        File pathChecker = new File(path);
        if (pathChecker.exists()) {
            ProfilePicturesFolder.setPath(reformatPath(path));
            model.setProfilePicsPath(reformatPath(path));
        } else {
            throw new CommandException(MESSAGE_FAILURE);
        }
        return new CommandResult(MESSAGE_SUCCESS);
    }

    /**
     * Reformats the path to suit the application
     * @return modified path to suit the application
     */
    private String reformatPath(String path) {
        path = path.replaceAll("\\\\", "/");
        String lastChar = path.substring(path.length() - 1);
        if (!lastChar.equals("/")) {
            path = path.concat("/");
        }
        return path;
    }
}
```
###### \java\seedu\address\logic\commands\UnbanCommand.java
``` java
/**
 * Removes a person identified using it's last displayed index from the blacklist.
 */
public class UnbanCommand extends UndoableCommand {

    public static final String COMMAND_WORD = "unban";

    public static final String MESSAGE_USAGE = COMMAND_WORD
            + ": Unbans the currently selected person or the person identified by the index number used in the last "
            + "person listing from blacklist.\n"
            + "Parameters: INDEX (optional, must be a positive integer if present)\n"
            + "Example: " + COMMAND_WORD + " 1";
    public static final String MESSAGE_UNBAN_PERSON_SUCCESS = "Removed %1$s from BLACKLIST";
    public static final String MESSAGE_UNBAN_PERSON_FAILURE = "%1$s is not BLACKLISTED!";

    private final ReadOnlyPerson personToUnban;

    public UnbanCommand() throws CommandException {
        personToUnban = selectPersonForCommand();
    }

    public UnbanCommand(Index targetIndex) throws CommandException {
        personToUnban = selectPersonForCommand(targetIndex);
    }

    @Override
    public CommandResult executeUndoableCommand() throws CommandException {
        ReadOnlyPerson targetPerson = personToUnban;

        try {
            if (personToUnban.isBlacklisted()) {
                targetPerson = model.removeBlacklistedPerson(personToUnban);
            } else {
                throw new CommandException(String.format(MESSAGE_UNBAN_PERSON_FAILURE, personToUnban.getName()));
            }
        } catch (PersonNotFoundException e) {
            assert false : "The target person is not in blacklist";
        }

        ListObserver.updateCurrentFilteredList(PREDICATE_SHOW_ALL_PERSONS);

        String currentList = ListObserver.getCurrentListName();

        return new CommandResult(currentList + String.format(MESSAGE_UNBAN_PERSON_SUCCESS, targetPerson.getName()));
    }

    @Override
    public boolean equals(Object other) {
        return other == this // short circuit if same object
                || (other instanceof UnbanCommand // instanceof handles nulls
                && this.personToUnban.equals(((UnbanCommand) other).personToUnban)); // state check
    }
}
```
###### \java\seedu\address\logic\commands\WhitelistCommand.java
``` java
/**
 * Lists all persons who have cleared their debts.
 */
public class WhitelistCommand extends Command {

    public static final String COMMAND_WORD = "whitelist";
    public static final String COMMAND_WORD_ALIAS = "wl";

    public static final String MESSAGE_SUCCESS = "Listed all debtors "
            + "who have cleared their debts";

    @Override
    public CommandResult execute() {
        requireNonNull(model);
        model.deselectPerson();
        model.changeListTo(COMMAND_WORD);
        model.updateFilteredWhitelistedPersonList(PREDICATE_SHOW_ALL_WHITELISTED_PERSONS);
        String currentList = ListObserver.getCurrentListName();
        return new CommandResult(currentList + MESSAGE_SUCCESS);
    }
}
```
###### \java\seedu\address\logic\Logic.java
``` java
    /** Returns an unmodifiable view of the filtered list of blacklisted persons */
    ObservableList<ReadOnlyPerson> getFilteredBlacklistedPersonList();

    /** Returns an unmodifiable view of the filtered list of whitelisted persons */
    ObservableList<ReadOnlyPerson> getFilteredWhitelistedPersonList();
```
###### \java\seedu\address\logic\LogicManager.java
``` java
    @Override
    public ObservableList<ReadOnlyPerson> getFilteredBlacklistedPersonList() {
        return model.getFilteredBlacklistedPersonList();
    }

    @Override
    public ObservableList<ReadOnlyPerson> getFilteredWhitelistedPersonList() {
        return model.getFilteredWhitelistedPersonList();
    }
```
###### \java\seedu\address\logic\parser\AddPictureCommandParser.java
``` java
/**
 * Parses input arguments and creates a new {@code AddPictureCommand} object
 */
public class AddPictureCommandParser implements Parser<AddPictureCommand> {

    /**
     * Parses the given {@code String} of arguments in the context of the AddPictureCommand
     * and returns an AddPictureCommand object for execution.
     * @throws ParseException if the user input does not conform the expected format
     */
    public AddPictureCommand parse(String args) throws ParseException, CommandException {
        try {
            if (args.trim().equals("")) {
                return new AddPictureCommand();
            } else {
                Index index = ParserUtil.parseIndex(args);
                return new AddPictureCommand(index);
            }
        } catch (IllegalValueException ive) {
            throw new ParseException(
                    String.format(MESSAGE_INVALID_COMMAND_FORMAT, AddPictureCommand.MESSAGE_USAGE));
        }
    }
}
```
###### \java\seedu\address\logic\parser\BanCommandParser.java
``` java
/**
 * Parses input arguments and creates a new {@code BanCommand} object
 */
public class BanCommandParser implements Parser<BanCommand> {

    /**
     * Parses the given {@code String} of arguments in the context of the BanCommand
     * and returns an BanCommand object for execution.
     * @throws ParseException if the user input does not conform the expected format
     */
    public BanCommand parse(String args) throws ParseException, CommandException {
        try {
            if (args.trim().equals("")) {
                return new BanCommand();
            } else {
                Index index = ParserUtil.parseIndex(args);
                return new BanCommand(index);
            }
        } catch (IllegalValueException ive) {
            throw new ParseException(
                    String.format(MESSAGE_INVALID_COMMAND_FORMAT, BanCommand.MESSAGE_USAGE));
        }
    }
}
```
###### \java\seedu\address\logic\parser\DeletePictureCommandParser.java
``` java
/**
 * Parses input arguments and creates a new {@code DeletePictureCommand} object
 */
public class DeletePictureCommandParser implements Parser<DeletePictureCommand> {

    /**
     * Parses the given {@code String} of arguments in the context of the DeletePictureCommand
     * and returns an DeletePictureCommand object for execution.
     * @throws ParseException if the user input does not conform the expected format
     */
    public DeletePictureCommand parse(String args) throws ParseException, CommandException {
        try {
            if (args.trim().equals("")) {
                return new DeletePictureCommand();
            } else {
                Index index = ParserUtil.parseIndex(args);
                return new DeletePictureCommand(index);
            }
        } catch (IllegalValueException ive) {
            throw new ParseException(
                    String.format(MESSAGE_INVALID_COMMAND_FORMAT, DeletePictureCommand.MESSAGE_USAGE));
        }
    }
}
```
###### \java\seedu\address\logic\parser\RepaidCommandParser.java
``` java
/**
 * Parses input arguments and creates a new {@code RepaidCommand} object
 */
public class RepaidCommandParser implements Parser<RepaidCommand> {
    /**
    * Parses the given {@code String} of arguments in the context of the RepaidCommand
    * and returns an RepaidCommand object for execution.
    * @throws ParseException if the user input does not conform the expected format
    */
    public RepaidCommand parse(String args) throws ParseException, CommandException {
        try {
            if (args.trim().equals("")) {
                return new RepaidCommand();
            } else {
                Index index = ParserUtil.parseIndex(args);
                return new RepaidCommand(index);
            }
        } catch (IllegalValueException ive) {
            throw new ParseException(
                    String.format(MESSAGE_INVALID_COMMAND_FORMAT, RepaidCommand.MESSAGE_USAGE));
        }
    }
}
```
###### \java\seedu\address\logic\parser\SetPathCommandParser.java
``` java
/**
 * Parses input arguments and creates a new {@code SetPathCommand} object
 */
public class SetPathCommandParser {

    public SetPathCommand parse(String args) throws ParseException {
        return new SetPathCommand(args.trim());
    }
}
```
###### \java\seedu\address\logic\parser\UnbanCommandParser.java
``` java
/**
 * Parses input arguments and creates a new {@code UnbanCommand} object
 */
public class UnbanCommandParser implements Parser<UnbanCommand> {
    /**
     * Parses the given {@code String} of arguments in the context of the UnbanCommand
     * and returns an UnbanCommand object for execution.
     * @throws ParseException if the user input does not conform the expected format
     */
    public UnbanCommand parse(String args) throws ParseException, CommandException {
        try {
            if (args.trim().equals("")) {
                return new UnbanCommand();
            } else {
                Index index = ParserUtil.parseIndex(args);
                return new UnbanCommand(index);
            }
        } catch (IllegalValueException ive) {
            throw new ParseException(
                    String.format(MESSAGE_INVALID_COMMAND_FORMAT, UnbanCommand.MESSAGE_USAGE));
        }
    }
}

```
###### \java\seedu\address\model\AddressBook.java
``` java
    /**
     * Adds a person to the blacklist in the address book.
     * @return the newly blacklisted person
     */
    public ReadOnlyPerson addBlacklistedPerson(ReadOnlyPerson p) {
        int index;
        index = persons.getIndexOf(p);

        Person newBlacklistedPerson = new Person(p);
        newBlacklistedPerson.setIsBlacklisted(true);
        try {
            updatePerson(p, newBlacklistedPerson);
        } catch (DuplicatePersonException e) {
            throw new AssertionError("The target person cannot be a duplicate");
        } catch (PersonNotFoundException e) {
            throw new AssertionError("This is not possible as prior checks have been done");
        }

        return persons.getReadOnlyPerson(index);
    }

    /**
     * Adds a person to the whitelist in the address book.
     * @return the newly whitelisted person
     */
    public ReadOnlyPerson addWhitelistedPerson(ReadOnlyPerson p) {
        int index;
        index = persons.getIndexOf(p);

        Person newWhitelistedPerson = new Person(p);
        newWhitelistedPerson.setIsWhitelisted(true);
        try {
            updatePerson(p, newWhitelistedPerson);
        } catch (DuplicatePersonException e) {
            throw new AssertionError("The target person cannot be a duplicate");
        } catch (PersonNotFoundException e) {
            throw new AssertionError("This is not possible as prior checks have been done");
        }
        return persons.getReadOnlyPerson(index);
    }

```
###### \java\seedu\address\model\AddressBook.java
``` java
    /**
     * Updates {@code key} to exclude {@code key} from the blacklist in this {@code AddressBook}.
     * @return the newly blacklisted person.
     * @throws PersonNotFoundException if the {@code key} is not in this {@code AddressBook}.
     */
    public ReadOnlyPerson removeBlacklistedPerson(ReadOnlyPerson key) throws PersonNotFoundException {
        int index;
        index = persons.getIndexOf(key);

        Person newUnBlacklistedPerson = new Person(key);
        newUnBlacklistedPerson.setIsBlacklisted(false);

        if (newUnBlacklistedPerson.getDebt().toNumber() == 0) {
            newUnBlacklistedPerson.setIsWhitelisted(true);
        }

        persons.remove(key);

        try {
            persons.add(index, newUnBlacklistedPerson);
        } catch (DuplicatePersonException e) {
            assert false : "This is not possible as prior checks have"
                    + " been done to ensure AddressBook does not have duplicate persons";
        }

        return persons.getReadOnlyPerson(index);
    }

    /**
     * Updates {@code key} to exclude {@code key} from the whitelist in this {@code AddressBook}.
     * @return the newly whitelisted person
     * @throws PersonNotFoundException if the {@code key} is not in this {@code AddressBook}.
     */
    public ReadOnlyPerson removeWhitelistedPerson(ReadOnlyPerson key) throws PersonNotFoundException {
        int index;
        index = persons.getIndexOf(key);

        Person newWhitelistedPerson = new Person(key);
        newWhitelistedPerson.setIsWhitelisted(false);
        persons.remove(key);
        try {
            persons.add(index, newWhitelistedPerson);
        } catch (DuplicatePersonException e) {
            assert false : "This is not possible as prior checks have"
                    + " been done to ensure AddressBook does not have duplicate persons";
        }
        return persons.getReadOnlyPerson(index);
    }

```
###### \java\seedu\address\model\AddressBook.java
``` java
    /**
     * Resets person's debt field to zero, in the masterlist of the addressbook.
     * @return ReadOnly existingPerson
     * @throws PersonNotFoundException if person does not exist in list.
     */
    public ReadOnlyPerson resetPersonDebt(ReadOnlyPerson p) throws PersonNotFoundException {
        int index;
        index = persons.getIndexOf(p);

        Person existingPerson = new Person(p);
        try {
            existingPerson.setDebt(new Debt(Debt.DEBT_ZER0_VALUE));
        } catch (IllegalValueException e) {
            assert false : "The target value cannot be of illegal value";
        }

        persons.remove(p);

        try {
            persons.add(index, existingPerson);
        } catch (DuplicatePersonException dpe) {
            assert false : "There should be no duplicate when resetting the debt of a person";
        }

        return persons.getReadOnlyPerson(index);
    }

    /**
     * Resets person's {@code dateRepaid} field to current date, in the masterlist of the addressbook.
     * @return ReadOnly existingPerson
     * @throws PersonNotFoundException if person does not exist in list.
     */
    public ReadOnlyPerson setDateRepaid(ReadOnlyPerson p) throws PersonNotFoundException {
        int index;
        index = persons.getIndexOf(p);

        Person existingPerson = new Person(p);
        existingPerson.setDateRepaid(new DateRepaid(formatDate(new Date())));

        persons.remove(p);

        try {
            persons.add(index, existingPerson);
        } catch (DuplicatePersonException dpe) {
            assert false : "There should be no duplicate when resetting the date repaid field of a person";
        }

        return persons.getReadOnlyPerson(index);
    }

```
###### \java\seedu\address\model\AddressBook.java
``` java
    /**
     * Adds the picture of the person into app database and sets the person's display picture boolean status to true
     * @return updated person
     */
    public ReadOnlyPerson addProfilePic(ReadOnlyPerson person) {
        int index;
        index = persons.getIndexOf(person);

        Person newUpdatedPerson = new Person(person);
        newUpdatedPerson.setHasDisplayPicture(true);
        try {
            updatePerson(person, newUpdatedPerson);
        } catch (DuplicatePersonException e) {
            throw new AssertionError("The target person cannot be a duplicate");
        } catch (PersonNotFoundException e) {
            throw new AssertionError("This is not possible as prior checks have been done");
        }

        return persons.getReadOnlyPerson(index);
    }

    /**
     * Sets the person's display picture boolean status to false
     * @return updated person
     */
    public ReadOnlyPerson removeProfilePic(ReadOnlyPerson person) {
        int index;
        index = persons.getIndexOf(person);

        Person newUpdatedPerson = new Person(person);
        newUpdatedPerson.setHasDisplayPicture(false);
        try {
            updatePerson(person, newUpdatedPerson);
        } catch (DuplicatePersonException e) {
            throw new AssertionError("The target person cannot be a duplicate");
        } catch (PersonNotFoundException e) {
            throw new AssertionError("This is not possible as prior checks have been done");
        }

        return persons.getReadOnlyPerson(index);
    }
```
###### \java\seedu\address\model\AddressBook.java
``` java

    @Override
    public ObservableList<ReadOnlyPerson> getBlacklistedPersonList() {
        return persons.asObservableBlacklist();
    }
    @Override
    public ObservableList<ReadOnlyPerson> getWhitelistedPersonList() {
        return persons.asObservableWhitelist();
    }

```
###### \java\seedu\address\model\Model.java
``` java
    /** {@code Predicate} that always evaluate to true */
    Predicate<ReadOnlyPerson> PREDICATE_SHOW_ALL_BLACKLISTED_PERSONS = unused -> true;

    /** {@code Predicate} that always evaluate to true */
    Predicate<ReadOnlyPerson> PREDICATE_SHOW_ALL_WHITELISTED_PERSONS = unused -> true;

```
###### \java\seedu\address\model\Model.java
``` java
    /** Returns the name of current displayed list */
    String getCurrentListName();

    /** Sets the name of current displayed list */
    void setCurrentListName(String currentList);

```
###### \java\seedu\address\model\Model.java
``` java
    /** Deletes the given person from blacklist and returns the deleted person */
    ReadOnlyPerson removeBlacklistedPerson(ReadOnlyPerson target) throws PersonNotFoundException;

    /** Removes the given person from whitelist and returns the updated person */
    ReadOnlyPerson removeWhitelistedPerson(ReadOnlyPerson target) throws PersonNotFoundException;

```
###### \java\seedu\address\model\Model.java
``` java
    /** Adds the given person into blacklist and returns the added person*/
    ReadOnlyPerson addBlacklistedPerson(ReadOnlyPerson person);

    /** Adds the given person into whitelist and returns the added person */
    ReadOnlyPerson addWhitelistedPerson(ReadOnlyPerson person);

```
###### \java\seedu\address\model\Model.java
``` java
    /** Returns an unmodifiable view of the blacklisted filtered person list */
    ObservableList<ReadOnlyPerson> getFilteredBlacklistedPersonList();

    /** Returns an unmodifiable view of the whitelisted filtered person list */
    ObservableList<ReadOnlyPerson> getFilteredWhitelistedPersonList();

```
###### \java\seedu\address\model\Model.java
``` java
    /**
     * Updates the filter of the filtered blacklisted person list to filter by the given {@code predicate}.
     * @return size of current displayed filtered list.
     * @throws NullPointerException if {@code predicate} is null.
     */
    int updateFilteredBlacklistedPersonList(Predicate<ReadOnlyPerson> predicate);

    /**
     * Updates the filter of the filtered whitelisted person list to filter by the given {@code predicate}.
     * @return size of current displayed filtered list.
     * @throws NullPointerException if {@code predicate} is null.
     */
    int updateFilteredWhitelistedPersonList(Predicate<ReadOnlyPerson> predicate);

```
###### \java\seedu\address\model\Model.java
``` java
    /**
     * Updates the list shown in Person List Panel to the requested list.
     */
    void changeListTo(String listName);

```
###### \java\seedu\address\model\Model.java
``` java
    /**
     * Sets the path to the specified profile pictures folder in {@code userPrefs}
     */
    void setProfilePicsPath(String path);

    /**
     * Adds the picture of the person into app database and sets the person's display picture boolean status to true
     * @return updated {@code ReadOnlyPerson}.
     */
    ReadOnlyPerson addProfilePicture(ReadOnlyPerson person) throws ProfilePictureNotFoundException;

    /**
     * Sets the person's display picture boolean status to false.
     * @return updated {@code ReadOnlyPerson}.
     */
    ReadOnlyPerson removeProfilePicture(ReadOnlyPerson person) throws ProfilePictureNotFoundException;

```
###### \java\seedu\address\model\ModelManager.java
``` java
    /**
     * @return String value of the current displayed list
     */
    @Override
    public String getCurrentListName() {
        return currentList;
    }

    /**
     * Sets String value of the current displayed list using value of {@param currentList}
     */
    @Override
    public void setCurrentListName(String currentList) {
        this.currentList = currentList;
    }

```
###### \java\seedu\address\model\ModelManager.java
``` java
    /**
     * Removes a specific person from blacklist in the AddressBook.
     * @param target to be removed from blacklist.
     * @return removedBlacklistedPerson
     * @throws PersonNotFoundException if no person is found.
     */
    @Override
    public synchronized ReadOnlyPerson removeBlacklistedPerson(ReadOnlyPerson target) throws PersonNotFoundException {
        ReadOnlyPerson removedBlacklistedPerson = addressBook.removeBlacklistedPerson(target);
        updateFilteredBlacklistedPersonList(PREDICATE_SHOW_ALL_BLACKLISTED_PERSONS);
        indicateAddressBookChanged();
        return removedBlacklistedPerson;
    }

    /**
     * Deletes a specific person from whitelist in the AddressBook.
     * @param target to be removed from whitelist.
     * @return removedBlacklistedPerson
     * @throws PersonNotFoundException if no person is found.
     */
    @Override
    public synchronized ReadOnlyPerson removeWhitelistedPerson(ReadOnlyPerson target) throws PersonNotFoundException {
        ReadOnlyPerson whitelistedPerson = addressBook.removeWhitelistedPerson(target);
        updateFilteredWhitelistedPersonList(PREDICATE_SHOW_ALL_WHITELISTED_PERSONS);
        indicateAddressBookChanged();
        return whitelistedPerson;
    }

```
###### \java\seedu\address\model\ModelManager.java
``` java
    /**
     * Adds a specific person to blacklist in the AddressBook.
     * @param person to be updated.
     * @return newBlacklistedPerson
     * @throws DuplicatePersonException if this operation causes a contact to be a duplicate of another.
     */
    @Override
    public synchronized ReadOnlyPerson addBlacklistedPerson(ReadOnlyPerson person) {
        ReadOnlyPerson newBlacklistPerson = person;

        if (person.isWhitelisted()) {
            try {
                newBlacklistPerson = addressBook.removeWhitelistedPerson(newBlacklistPerson);
            } catch (PersonNotFoundException e) {
                assert false : "This person cannot be missing from addressbook";
            }
        }
        newBlacklistPerson = addressBook.addBlacklistedPerson(newBlacklistPerson);
        updateFilteredBlacklistedPersonList(PREDICATE_SHOW_ALL_BLACKLISTED_PERSONS);
        indicateAddressBookChanged();
        return newBlacklistPerson;
    }

    /**
     * Adds a specific person to whitelist in the AddressBook.
     * @param person to be updated.
     * @return whitelistedPerson
     * @throws DuplicatePersonException if this operation causes a contact to be a duplicate of another.
     */
    @Override
    public synchronized ReadOnlyPerson addWhitelistedPerson(ReadOnlyPerson person) {
        ReadOnlyPerson whitelistedPerson = person;

        try {
            whitelistedPerson = addressBook.resetPersonDebt(person);
            whitelistedPerson = addressBook.setDateRepaid(whitelistedPerson);
        } catch (PersonNotFoundException e) {
            assert false : "This person cannot be missing from addressbook";
        }

        if (!whitelistedPerson.isBlacklisted()) {
            whitelistedPerson = addressBook.addWhitelistedPerson(whitelistedPerson);
        }
        updateFilteredWhitelistedPersonList(PREDICATE_SHOW_ALL_WHITELISTED_PERSONS);
        indicateAddressBookChanged();
        return whitelistedPerson;
    }

```
###### \java\seedu\address\model\ModelManager.java
``` java
    /**
     * Reads the masterlist and updates the blacklist accordingly.
     */
    public void syncBlacklist() {
        filteredBlacklistedPersons = new FilteredList<>(this.addressBook.getBlacklistedPersonList());
    }

    /**
     * Reads the masterlist and updates the whitelist accordingly.
     */
    public void syncWhitelist() {
        filteredWhitelistedPersons = new FilteredList<>(this.addressBook.getWhitelistedPersonList());
    }

```
###### \java\seedu\address\model\ModelManager.java
``` java
    @Override
    public void changeListTo(String listName) {
        setCurrentListName(listName);
        raise(new ChangeInternalListEvent(listName));
    }

```
###### \java\seedu\address\model\ModelManager.java
``` java
    /**
     * Sets the path to the specified profile pictures folder in {@code userPrefs}
     */
    @Override
    public void setProfilePicsPath(String path) {
        userPrefs.setProfilePicturesFolderPath(path);
    }

    /**
     * Adds the picture of the person into app database and sets the person's display picture boolean status to true
     * @return updated {@code ReadOnlyPerson}.
     */
    @Override
    public ReadOnlyPerson addProfilePicture(ReadOnlyPerson person) throws ProfilePictureNotFoundException {
        String imageName = person.getName().toString().replaceAll("\\s+", "");
        File imageFile = new File(ProfilePicturesFolder.getPath() + imageName + JPG_EXTENSION);

        if (imageFile.exists()) {
            ReadOnlyPerson updatedPerson = addressBook.addProfilePic(person);
            indicateAddressBookChanged();
            return updatedPerson;
        } else {
            throw new ProfilePictureNotFoundException();
        }
    }

    /**
     * Sets the person's display picture boolean status to false
     * @return updated {@code ReadOnlyPerson}.
     */
    @Override
    public ReadOnlyPerson removeProfilePicture(ReadOnlyPerson person) throws ProfilePictureNotFoundException {
        if (person.hasDisplayPicture()) {
            ReadOnlyPerson updatedPerson = addressBook.removeProfilePic(person);
            indicateAddressBookChanged();
            return updatedPerson;
        } else {
            throw new ProfilePictureNotFoundException();
        }
    }
```
###### \java\seedu\address\model\ModelManager.java
``` java
    /**
     * Returns an unmodifiable view of the blacklist of {@code ReadOnlyPerson} backed by the internal list of
     * {@code addressBook}
     */
    @Override
    public ObservableList<ReadOnlyPerson> getFilteredBlacklistedPersonList() {
        setCurrentListName("blacklist");
        syncBlacklist();
        filteredBlacklistedPersons.setPredicate(currentPredicate);
        return FXCollections.unmodifiableObservableList(filteredBlacklistedPersons);
    }

    /**
     * Returns an unmodifiable view of the whitelist of {@code ReadOnlyPerson} backed by the internal list of
     * {@code addressBook}
     */
    @Override
    public ObservableList<ReadOnlyPerson> getFilteredWhitelistedPersonList() {
        setCurrentListName("whitelist");
        syncWhitelist();
        filteredWhitelistedPersons.setPredicate(currentPredicate);
        return FXCollections.unmodifiableObservableList(filteredWhitelistedPersons);
    }

```
###### \java\seedu\address\model\ModelManager.java
``` java
    /**
     * Filters {@code filteredBlacklistedPersons} according to given {@param predicate}
     * @return size of current displayed filtered list.
     */
    @Override
    public int updateFilteredBlacklistedPersonList(Predicate<ReadOnlyPerson> predicate) {
        requireNonNull(predicate);
        currentPredicate = predicate;
        filteredBlacklistedPersons.setPredicate(predicate);
        return filteredBlacklistedPersons.size();
    }

    /**
     * Filters {@code filteredWhitelistedPersons} according to given {@param predicate}
     * @return size of current displayed filtered list.
     */
    @Override
    public int updateFilteredWhitelistedPersonList(Predicate<ReadOnlyPerson> predicate) {
        requireNonNull(predicate);
        currentPredicate = predicate;
        filteredWhitelistedPersons.setPredicate(predicate);
        return filteredWhitelistedPersons.size();
    }

```
###### \java\seedu\address\model\ModelManager.java
``` java
    @Subscribe
    public void handleMissingDisplayPictureEvent(MissingDisplayPictureEvent event) {
        try {
            removeProfilePicture(event.getPerson());
        } catch (ProfilePictureNotFoundException e) {
            assert false : "This is not possible as person's hasDisplayPicture boolean value must be true";
        }
    }


}
```
###### \java\seedu\address\model\person\DateRepaid.java
``` java
/**
 * Represents the date of when the Person was sent to the whitelist in the address book, i.e. the date
 * the Person repaid his debt completely.
 * Guarantees: immutable;
 */
public class DateRepaid {

    public static final String NO_DATE_REPAID = "NOT REPAID";

    public final String value;

    public DateRepaid() {
        value = NO_DATE_REPAID;
    }

    /**
     * Creates a copy of the DateRepaid object with a set date.
     * @param date must be a valid date
     */
    public DateRepaid(String date) {
        value = date;
    }

    @Override
    public String toString() {
        return value;
    }

    @Override
    public boolean equals(Object other) {
        return other == this // short circuit if same object
                || (other instanceof DateRepaid // instanceof handles nulls
                && this.value.equals(((DateRepaid) other).value)); // state check
    }

    @Override
    public int hashCode() {
        return value.hashCode();
    }
}
```
###### \java\seedu\address\model\person\exceptions\ProfilePictureNotFoundException.java
``` java
/**
 * Signals that the operation is unable to find the specified person's profile picture.
 */
public class ProfilePictureNotFoundException extends Exception {
}
```
###### \java\seedu\address\model\person\Person.java
``` java
    /**
     * Returns boolean status of a person's blacklist-status.
     */
    @Override
    public boolean isBlacklisted() {
        return isBlacklisted;
    }

    /**
     * Sets boolean status of a person's blacklist-status using the value of {@param isBlacklisted}.
     */
    @Override
    public void setIsBlacklisted(boolean isBlacklisted) {
        this.isBlacklisted = isBlacklisted;
    }

    /**
     * Returns boolean status of a person's whitelist-status.
     */
    @Override
    public boolean isWhitelisted() {
        return isWhitelisted;
    }

    /**
     * Sets boolean status of a person's whitelist-status using the value of {@param isWhitelisted}.
     */
    @Override
    public void setIsWhitelisted(boolean isWhitelisted) {
        this.isWhitelisted = isWhitelisted;
    }

```
###### \java\seedu\address\model\person\Person.java
``` java
    /**
     * Returns boolean status of a person's display picture status.
     */
    @Override
    public boolean hasDisplayPicture() {
        return hasDisplayPicture;
    }

    /**
     * Sets boolean status of a person's display picture status using the value of {@param hasDisplayPicture}.
     */
    @Override
    public void setHasDisplayPicture(boolean hasDisplayPicture) {
        this.hasDisplayPicture = hasDisplayPicture;
    }

    /**
     * Sets date repaid of a person in the given {@code dateRepaid}.
     * @param dateRepaid must not be null.
     */
    public void setDateRepaid(DateRepaid dateRepaid) {
        this.dateRepaid.set(requireNonNull(dateRepaid));
    }

    @Override
    public ObjectProperty<DateRepaid> dateRepaidProperty() {
        return dateRepaid;
    }

    @Override
    public DateRepaid getDateRepaid() {
        return dateRepaid.get();
    }
```
###### \java\seedu\address\model\person\ReadOnlyPerson.java
``` java
    /**
     * Returns true if person is blacklisted.
     */
    boolean isBlacklisted();

    /**
     * {@param} is {@code boolean} value.
     * Sets {@code boolean} variable as the value of {@param isBlacklisted}
     */
    void setIsBlacklisted(boolean isBlacklisted);

    /**
     * Returns true if person is whitelisted.
     */
    boolean isWhitelisted();

    /**
     * {@param} is {@code boolean} value.
     * Sets {@code boolean} variable as the value of {@param isWhitelisted}
     */
    void setIsWhitelisted(boolean isWhitelisted);

    /**
     * Returns true if person has display picture.
     */
    boolean hasDisplayPicture();

    /**
     * {@param} is {@code boolean} value.
     * Sets {@code boolean} variable as the value of {@param hasDisplayPicture}
     */
    void setHasDisplayPicture(boolean hasDisplayPicture);
```
###### \java\seedu\address\model\person\UniquePersonList.java
``` java
    /**
     * Returns index of {@code ReadOnlyPerson} in list.
     */
    public int getIndexOf(ReadOnlyPerson key) {
        return internalList.indexOf(key);
    }

    /**
     * Returns {@code ReadOnlyPerson} in list from given {@param index}.
     */
    public ReadOnlyPerson getReadOnlyPerson(int index) {
        return mappedList.get(index);
    }
```
###### \java\seedu\address\model\person\UniquePersonList.java
``` java
    /**
     * Returns the backing list as an unmodifiable {@code ObservableList}.
     */
    public ObservableList<ReadOnlyPerson> asObservableBlacklist() {
        return FXCollections.unmodifiableObservableList(mappedList.stream()
                .filter(person -> person.isBlacklisted()).collect(toCollection(FXCollections::observableArrayList)));
    }

    /**
     * Returns the backing list as an unmodifiable {@code ObservableList}.
     */
    public ObservableList<ReadOnlyPerson> asObservableWhitelist() {
        return FXCollections.unmodifiableObservableList(mappedList.stream()
                .filter(person -> person.isWhitelisted()).collect(toCollection(FXCollections::observableArrayList)));
    }
```
###### \java\seedu\address\model\ReadOnlyAddressBook.java
``` java
    /**
     * Returns an unmodifiable view of the blacklisted persons list.
     * This list will not contain any duplicate persons.
     */
    ObservableList<ReadOnlyPerson> getBlacklistedPersonList();

    /**
     * Returns an unmodifiable view of the whitelisted persons list.
     * This list will not contain any duplicate persons.
     */
    ObservableList<ReadOnlyPerson> getWhitelistedPersonList();

```
###### \java\seedu\address\model\UserPrefs.java
``` java
    /**
     * @return path of the profile pictures folder that resides in user's workspace
     */
    public String getProfilePicturesFolderPath() {
        return profilePicturesFolderPath;
    }

    /**
     * Sets the path of the profile pictures folder indicated by user
     * @param path points to the folder that resides in user's workspace
     */
    public void setProfilePicturesFolderPath(String path) {
        this.profilePicturesFolderPath = path;
    }

```
###### \java\seedu\address\storage\XmlSerializableAddressBook.java
``` java
    /**
     * Returns {@code ObservableList} of blacklisted persons.
     */
    @Override
    public ObservableList<ReadOnlyPerson> getBlacklistedPersonList() {
        ObservableList<ReadOnlyPerson> persons = getPersonList();
        ObservableList<ReadOnlyPerson> blacklistedPersons = persons.stream()
                .filter(person -> person.isBlacklisted())
                .collect(Collectors.toCollection(FXCollections::observableArrayList));
        return FXCollections.unmodifiableObservableList(blacklistedPersons);
    }

    /**
     * Returns {@code ObservableList} of whitelisted persons.
     */
    @Override
    public ObservableList<ReadOnlyPerson> getWhitelistedPersonList() {
        ObservableList<ReadOnlyPerson> persons = getPersonList();
        ObservableList<ReadOnlyPerson> whitelistedPersons = persons.stream()
                .filter(person -> person.isWhitelisted())
                .collect(Collectors.toCollection(FXCollections::observableArrayList));
        return FXCollections.unmodifiableObservableList(whitelistedPersons);
    }

```
###### \java\seedu\address\ui\DebtorProfilePicture.java
``` java
/**
 * Displays profile picture of each debtor
 */
public class DebtorProfilePicture extends UiPart<Region> {
    public static final String FXML = "DebtorProfilePicture.fxml";
    public static final String DEFAULT_PROFILEPIC_PATH = "/images/profilePics/unknown.jpg";
    public static final String JPG_EXTENSION = ".jpg";

    @FXML
    private ImageView profilePic = new ImageView();
    @FXML
    private AnchorPane profilePicPlaceHolder;

    public DebtorProfilePicture(ReadOnlyPerson person) {
        super(FXML);
        String imageName = person.getName().toString().replaceAll("\\s+", "");
        String imagePath = DEFAULT_PROFILEPIC_PATH;
        Image image = new Image(getClass().getResource(imagePath).toExternalForm());

        if (person.hasDisplayPicture()) {

            imagePath = ProfilePicturesFolder.getPath() + imageName + JPG_EXTENSION;
            File imageFile = new File(imagePath);

            if (!imageFile.exists()) {
                person.setHasDisplayPicture(false);
                raise(new MissingDisplayPictureEvent(person));
            } else {
                try {
                    image = new Image(imageFile.toURI().toURL().toExternalForm());
                } catch (MalformedURLException e) {
                    e.printStackTrace();
                }
            }
        }

        profilePic.setImage(image);
        profilePic.setFitWidth(450);
        profilePic.setFitHeight(450);
        profilePicPlaceHolder.setTopAnchor(this.getImageView(), 20.0);
        profilePicPlaceHolder.setRightAnchor(this.getImageView(), 50.0);
        registerAsAnEventHandler(this);
    }

    public ImageView getImageView() {
        return profilePic;
    }

}
```
###### \java\seedu\address\ui\InfoPanel.java
``` java
    /**
     * Resets the debtors profile picture to the latest one existing in folder
     * @param person the person whose person card is selected in the address book
     */
    private void resetDebtorProfilePicture(ReadOnlyPerson person) {
        debtorProfilePicture = new DebtorProfilePicture(person);
        profilePicPlaceholder.getChildren().add(debtorProfilePicture.getImageView());
    }

```
###### \java\seedu\address\ui\InfoPanel.java
``` java
    @Subscribe
    private void handleChangeInternalListEvent(ChangeInternalListEvent event) {
        unregisterAsAnEventHandler(this);
    }

```
###### \java\seedu\address\ui\MainWindow.java
``` java
    /**
     * Fills up the placeholders of PersonListPanel with the given list name.
     * Should only display welcome page without contacts.
     */
    void fillInnerPartsWithIndicatedList(String listName) {

        switch(listName) {

        case BlacklistCommand.COMMAND_WORD:
            personListPanel = new PersonListPanel(logic.getFilteredBlacklistedPersonList(), listName);
            break;
        case WhitelistCommand.COMMAND_WORD:
            personListPanel = new PersonListPanel(logic.getFilteredWhitelistedPersonList(), listName);
            break;
        case OverdueListCommand.COMMAND_WORD:
            personListPanel = new PersonListPanel(logic.getFilteredOverduePersonList(), listName);
            break;
        default:
            personListPanel = new PersonListPanel(logic.getFilteredPersonList(), ListCommand.COMMAND_WORD);
        }

        personListPanelPlaceholder.getChildren().clear();
        personListPanelPlaceholder.getChildren().add(personListPanel.getRoot());
        infoPanel = new InfoPanel(logic);
        infoPanelPlaceholder.getChildren().clear();
        infoPanelPlaceholder.getChildren().add(infoPanel.getRoot());
    }

```
###### \java\seedu\address\ui\UiManager.java
``` java
    /**
     * Handles change internal list event.
     * Displays the list that user requested(e.g masterlist, blacklist etc)
     */
    @Subscribe
    private void handleChangeInternalListEvent(ChangeInternalListEvent event) {
        logger.info(LogsCenter.getEventHandlingLogMessage(event));
        mainWindow.fillInnerPartsWithIndicatedList(event.getListName());
    }
}
```
###### \resources\view\DebtorProfilePicture.fxml
``` fxml

<?import javafx.scene.image.ImageView?>
<?import javafx.scene.layout.StackPane?>

<StackPane maxHeight="-Infinity" maxWidth="-Infinity" minHeight="-Infinity" minWidth="-Infinity" prefHeight="182.0" prefWidth="197.0" xmlns="http://javafx.com/javafx/8.0.141" xmlns:fx="http://javafx.com/fxml/1">
   <children>
      <ImageView fitHeight="187.0" fitWidth="200.0" pickOnBounds="true" preserveRatio="true" />
   </children>
</StackPane>
```
