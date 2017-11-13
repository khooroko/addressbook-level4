# jelneo
###### \java\seedu\address\commons\events\ui\ChangeToCommandBoxView.java
``` java
/**
 * Indicate request to change to command box view.
 */
public class ChangeToCommandBoxView extends BaseEvent {

    public ChangeToCommandBoxView () {
        setShowingLoginView(false);
    }

    @Override
    public String toString() {
        return this.getClass().getSimpleName();
    }
}
```
###### \java\seedu\address\commons\events\ui\ChangeToLoginViewEvent.java
``` java
/**
 * Indicates a request to display login text fields.
 */
public class ChangeToLoginViewEvent extends BaseEvent {

    public ChangeToLoginViewEvent () {
        setShowingLoginView(true);
    }

    @Override
    public String toString() {
        return this.getClass().getSimpleName();
    }
}
```
###### \java\seedu\address\commons\events\ui\LoginAppRequestEvent.java
``` java
/**
 * Indicates a request for App login.
 */
public class LoginAppRequestEvent extends BaseEvent {

    private boolean hasLoginSuccessfully = false;

    public LoginAppRequestEvent(boolean hasLoginSuccessfully) {
        this.hasLoginSuccessfully = hasLoginSuccessfully;
        setLoginStatus(hasLoginSuccessfully);
    }

    public boolean getLoginStatus() {
        return hasLoginSuccessfully;
    }

    @Override
    public String toString() {
        return this.getClass().getSimpleName();
    }
}
```
###### \java\seedu\address\commons\events\ui\LoginInputChangeEvent.java
``` java

import seedu.address.commons.events.BaseEvent;

/**
 * Indicates that login inputs have changed.
 */
public class LoginInputChangeEvent extends BaseEvent {
    @Override
    public String toString() {
        return this.getClass().getSimpleName();
    }
}
```
###### \java\seedu\address\commons\events\ui\LogoutAppRequestEvent.java
``` java
/**
 * Indicates a request for App logout.
 */
public class LogoutAppRequestEvent extends BaseEvent {

    private boolean hasLogoutSuccessfully = false;

    public LogoutAppRequestEvent(boolean hasLogoutSuccessfully) {
        this.hasLogoutSuccessfully = hasLogoutSuccessfully;
        setLogoutStatus(hasLogoutSuccessfully);
    }

    public boolean getLogoutStatus() {
        return hasLogoutSuccessfully;
    }

    @Override
    public String toString() {
        return this.getClass().getSimpleName();
    }
}
```
###### \java\seedu\address\commons\exceptions\UserNotFoundException.java
``` java
/**
 * Signals that the user does not exist.
 */
public class UserNotFoundException extends Exception {
    public UserNotFoundException() {
        super();
    }
}
```
###### \java\seedu\address\logic\commands\BorrowCommand.java
``` java
/**
 * Updates debt field when a person borrows more money.
 */
public class BorrowCommand extends UndoableCommand {
    public static final String COMMAND_WORD = "borrow";

    public static final String MESSAGE_USAGE = COMMAND_WORD + ": increase the debt of a person by "
            + "the amount of money entered.\n"
            + "Parameters: INDEX (optional, must be a positive integer if present)\n"
            + "AMOUNT (must have at least 1 digit and either a positive integer or a positive number with "
            + "two decimal places)\n"
            + "Example 1: " + COMMAND_WORD + " 1 120.50\n"
            + "Example 2: " + COMMAND_WORD + " 120.50";
    public static final String MESSAGE_BORROW_SUCCESS = "%1$s has borrowed $%2$s";
    public static final String MESSAGE_BORROW_BLACKLISTED_PERSON_FAILURE = "%1$s is prohibited from borrowing money!";

    private final ReadOnlyPerson personThatBorrowed;
    private final Debt amount;

    public BorrowCommand(Debt amount) throws CommandException {
        personThatBorrowed = selectPersonForCommand();
        this.amount = amount;
    }

    public BorrowCommand(Index targetIndex, Debt amount) throws CommandException {
        personThatBorrowed = selectPersonForCommand(targetIndex);
        this.amount = amount;
    }

    @Override
    public CommandResult executeUndoableCommand() throws CommandException {
        ReadOnlyPerson targetPerson = personThatBorrowed;
        if (personThatBorrowed.isBlacklisted()) {
            throw new CommandException(String.format(MESSAGE_BORROW_BLACKLISTED_PERSON_FAILURE,
                    targetPerson.getName()));
        }

        try {
            if (personThatBorrowed.isWhitelisted()) {
                targetPerson = model.removeWhitelistedPerson(personThatBorrowed);
            }
            targetPerson = model.addDebtToPerson(targetPerson, amount);
        } catch (PersonNotFoundException pnfe) {
            assert false : "The target person cannot be missing";
        }

        ListObserver.updateCurrentFilteredList(PREDICATE_SHOW_ALL_PERSONS);
        reselectPerson(targetPerson);

        String currentList = ListObserver.getCurrentListName();

        return new CommandResult(currentList
                + String.format(MESSAGE_BORROW_SUCCESS, targetPerson.getName(), amount));
    }

    @Override
    public boolean equals(Object other) {
        return other == this // short circuit if same object
                || (other instanceof BorrowCommand // instanceof handles nulls
                && this.personThatBorrowed.equals(((BorrowCommand) other).personThatBorrowed)); // state check
    }
}
```
###### \java\seedu\address\logic\commands\FilterCommand.java
``` java
/**
 * Filters contacts by tags in masterlist.
 */
public class FilterCommand extends Command {
    public static final String COMMAND_WORD = "filter";

    public static final String MESSAGE_USAGE = COMMAND_WORD + ": filters the address book by tag(s)\n"
            + "Parameters: [ TAG ]...\n"
            + "Example: " + COMMAND_WORD + " friendly tricky";
    public static final String MESSAGE_FILTER_ACKNOWLEDGEMENT = "Showing all contacts with the tag(s): %1$s\n%2$s ";

    private final List<String> tags;

    public FilterCommand(List<String> tags) {
        this.tags = tags;
    }

    @Override
    public CommandResult execute() {
        requireNonNull(model);

        model.deselectPerson();
        ListObserver.updateCurrentFilteredList(new PersonContainsTagPredicate(tags));

        String allTagKeywords = tags.toString();
        return new CommandResult(String.format(MESSAGE_FILTER_ACKNOWLEDGEMENT, allTagKeywords,
                getMessageForPersonListShownSummary(ListObserver.getCurrentFilteredList().size())));
    }

    @Override
    public boolean equals(Object other) {
        return other == this // short circuit if same object
                || (other instanceof FilterCommand // instanceof handles nulls
                && this.tags.equals(((FilterCommand) other).tags));
    }
}
```
###### \java\seedu\address\logic\commands\LoginCommand.java
``` java
/**
 * Handles login of a user.
 */
public class LoginCommand extends Command {

    public static final String COMMAND_WORD = "login";

    public static final String MESSAGE_LOGIN_REQUEST = "Please log in first";
    public static final String MESSAGE_USAGE = COMMAND_WORD + " USERNAME PASSWORD\nExample: "
            + COMMAND_WORD + " JohnDoe hiIAmJohnDoe123";
    public static final String MESSAGE_LOGIN_ACKNOWLEDGEMENT = "Login successful";
    public static final String MESSAGE_LOGIN_UNSUCCESSFUL = "Unable to log into Address Book";
    private static boolean isLoggedIn = false;
    private final Username username;
    private final Password password;

    public LoginCommand(Username username, Password password) {
        this.username = username;
        this.password = password;
    }

    /**
     * Checks if a user is logged in.
     * @return true is user is logged in, false otherwise
     */
    public static boolean isLoggedIn() {
        return isLoggedIn;
    }

    public static void setLoginStatus(boolean val) {
        isLoggedIn = val;
    }

    /**
     * Verifies if user is valid.
     * @throws UserNotFoundException if user is not valid
     * @throws IllegalValueException if either username or password do not meet the username or password requirements
     */
    private void verifyUser() throws UserNotFoundException, IllegalValueException {
        model.authenticateUser(username, password);
    }

    @Override
    public CommandResult execute() throws CommandException {
        try {
            verifyUser();
            return new CommandResult(MESSAGE_LOGIN_ACKNOWLEDGEMENT);
        } catch (UserNotFoundException unfe) {
            throw new CommandException(MESSAGE_LOGIN_UNSUCCESSFUL);
        } catch (IllegalValueException ive) {
            throw new CommandException(ive.getMessage());
        }
    }

    @Override
    public boolean equals(Object other) {
        return other == this // short circuit if same object
                || (other instanceof LoginCommand // instanceof handles nulls
                && this.username.equals(((LoginCommand) other).username)
                && this.password.equals(((LoginCommand) other).password));
    }
}
```
###### \java\seedu\address\logic\commands\PaybackCommand.java
``` java
/**
 * Updates debt field when a person repays an amount.
 */
public class PaybackCommand extends UndoableCommand {

    public static final String COMMAND_WORD = "payback";

    public static final String MESSAGE_USAGE = COMMAND_WORD + ": decrease the debt of a person by "
            + "the amount of money entered.\n"
            + "Parameters: INDEX (optional, must be a positive integer if present)\n"
            + "AMOUNT (must have at least 1 digit and either a positive integer or a positive number with "
            + "two decimal places)\n"
            + "Example 1: " + COMMAND_WORD + " 1 100.50\n"
            + "Example 2: " + COMMAND_WORD + " 100.50";

    public static final String MESSAGE_PAYBACK_SUCCESS = "%1$s has paid $%2$s back";
    public static final String MESSAGE_PAYBACK_FAILURE = "Amount paid back cannot be more than the debt owed";

    private final ReadOnlyPerson personThatPaidBack;
    private final Debt amount;

    public PaybackCommand(Debt amount) throws CommandException {
        personThatPaidBack = selectPersonForCommand();
        this.amount = amount;
    }

    public PaybackCommand(Index targetIndex, Debt amount) throws CommandException {
        personThatPaidBack = selectPersonForCommand(targetIndex);
        this.amount = amount;
    }

    @Override
    public CommandResult executeUndoableCommand() throws CommandException {
        ReadOnlyPerson targetPerson = personThatPaidBack;
        try {
            targetPerson = model.deductDebtFromPerson(targetPerson, amount);
            if (targetPerson.getDebt().toNumber() == 0 && !targetPerson.isBlacklisted()) {
                targetPerson = model.addWhitelistedPerson(targetPerson);
            }
        } catch (PersonNotFoundException pnfe) {
            assert false : "The target person cannot be missing";
        } catch (IllegalValueException ive) {
            throw new CommandException(ive.getMessage());
        }

        ListObserver.updateCurrentFilteredList(PREDICATE_SHOW_ALL_PERSONS);
        reselectPerson(targetPerson);

        String currentList = ListObserver.getCurrentListName();

        return new CommandResult(currentList
                + String.format(MESSAGE_PAYBACK_SUCCESS, targetPerson.getName(), amount));
    }

    @Override
    public boolean equals(Object other) {
        return other == this // short circuit if same object
                || (other instanceof PaybackCommand // instanceof handles nulls
                && this.personThatPaidBack.equals(((PaybackCommand) other).personThatPaidBack)
                && this.amount.equals(((PaybackCommand) other).amount)); // state check
    }
}
```
###### \java\seedu\address\logic\parser\BorrowCommandParser.java
``` java
/**
 * Parses input arguments and creates a new {@code BorrowCommand} object.
 */
public class BorrowCommandParser implements Parser<BorrowCommand> {

    public static final String ONE_OR_MORE_SPACES_REGEX = "\\s+";
    // arguments: index and debt amount borrowed
    private static final int MAXIMUM_ARGS_LENGTH = 2;
    private static final int ARGS_LENGTH_WITHOUT_INDEX = 1;


    /**
     * Parses the given {@code String} of arguments in the context of the BorrowCommand
     * and returns an BorrowCommand object for execution.
     * @throws ParseException if the user input does not conform the expected format
     */
    @Override
    public BorrowCommand parse(String args) throws ParseException, CommandException {
        requireNonNull(args);

        Index index;
        Debt debtAmount;
        String[] argsList = args.trim().split(ONE_OR_MORE_SPACES_REGEX);
        if (argsList.length > MAXIMUM_ARGS_LENGTH) {
            throw new ParseException(String.format(MESSAGE_INVALID_COMMAND_FORMAT, BorrowCommand.MESSAGE_USAGE));
        }

        if (argsList.length == ARGS_LENGTH_WITHOUT_INDEX) {
            try {
                debtAmount = new Debt(argsList[0]);
            } catch (IllegalValueException ive) {
                throw new ParseException(String.format(MESSAGE_INVALID_COMMAND_FORMAT, BorrowCommand.MESSAGE_USAGE));
            }

            return new BorrowCommand(debtAmount);
        } else {
            try {
                index = ParserUtil.parseIndex(argsList[0]);
                debtAmount = new Debt(argsList[1]);
            } catch (IllegalValueException ive) {
                throw new ParseException(String.format(MESSAGE_INVALID_COMMAND_FORMAT, BorrowCommand.MESSAGE_USAGE));
            }
        }

        return new BorrowCommand(index, debtAmount);
    }
}
```
###### \java\seedu\address\logic\parser\FilterCommandParser.java
``` java
/**
 * Parses input arguments and creates a new {@code FilterCommand} object.
 */
public class FilterCommandParser implements Parser<FilterCommand> {

    public static final String ONE_OR_MORE_SPACES_REGEX = "\\s+";

    /**
     * Parses the given {@code String} of arguments in the context of the FilterCommand
     * and returns an FilterCommand object for execution.
     * @throws ParseException if the user input does not conform the expected format
     */
    public FilterCommand parse(String args) throws ParseException {
        String trimmedArgs = args.trim();
        if (trimmedArgs.isEmpty()) {
            throw new ParseException(String.format(MESSAGE_INVALID_COMMAND_FORMAT, FilterCommand.MESSAGE_USAGE));
        }

        String[] tagKeywords = trimmedArgs.split(ONE_OR_MORE_SPACES_REGEX);
        for (String keyword : tagKeywords) {
            if (!Tag.isValidTagName(keyword)) {
                throw new ParseException(MESSAGE_TAG_CONSTRAINTS);
            }
        }

        return new FilterCommand(Arrays.asList(tagKeywords));
    }
}
```
###### \java\seedu\address\logic\parser\LoginCommandParser.java
``` java
/**
<<<<<<< Updated upstream
 * Parses input arguments and creates a new {@code LoginCommand} object
 */
public class LoginCommandParser implements Parser<LoginCommand> {

    public static final String ONE_OR_MORE_SPACES_REGEX = "\\s+";
    public static final String EMPTY_USERNAME_MESSAGE = "Username cannot be empty";
    public static final String EMPTY_PASSWORD_MESSAGE = "Password cannot be empty";

    /**
     * Parses the given {@code String} of arguments in the context of the LoginCommand
     * and returns a LoginCommand object for execution.
     * @throws ParseException if the user input does not conform to the expected format
     */
    public LoginCommand parse(String args) throws ParseException {
        requireNonNull(args);
        String trimmedArgs = args.trim();

        if (isShowingLoginView()) {
            return parseForGui(trimmedArgs);
        }

        try {
            String[] argsList = trimmedArgs.split(ONE_OR_MORE_SPACES_REGEX);
            // if incorrect number of arguments are supplied, throw ArrayOutOfBoundsException
            if (argsList.length != 2) {
                throw new ArrayIndexOutOfBoundsException();
            }
            Username username = ParserUtil.parseUsername(argsList[0]);
            Password password = ParserUtil.parsePassword(argsList[1]);
            return new LoginCommand(username, password);
        } catch (IllegalValueException ive) {
            throw new ParseException(ive.getMessage(), ive);
        } catch (ArrayIndexOutOfBoundsException aiofoe) {
            throw new ParseException(
                    String.format(MESSAGE_INVALID_COMMAND_FORMAT, LoginCommand.MESSAGE_USAGE));
        }
    }

    /**
     * This method is used to parse arguments for GUI login.
     * A separate {@code parse} method is needed to check if either username or password field is empty and display
     * the correct error message.
     * Parses the given {@code String} of arguments obtained from login GUI in the context of the LoginCommand
     * and returns an LoginCommand object for execution.
     * @throws ParseException if the user input does not conform to the expected format
     */
    public LoginCommand parseForGui(String args) throws ParseException {
        requireNonNull(args);

        // last element in argsList is ignored
        String[] argsList = args.split(Pattern.quote(SEPARATOR), -1);

        String enteredUsername = argsList[0];
        String enteredPassword = argsList[1];

        if (enteredUsername.isEmpty()) {
            throw new ParseException(EMPTY_USERNAME_MESSAGE);
        }
        if (enteredPassword.isEmpty()) {
            throw new ParseException(EMPTY_PASSWORD_MESSAGE);
        }
        try {
            Username username = new Username(enteredUsername);
            Password password = new Password(enteredPassword);
            return new LoginCommand(username, password);
        } catch (IllegalValueException ive) {
            throw new ParseException(ive.getMessage());
        }
    }
}
```
###### \java\seedu\address\logic\parser\ParserUtil.java
``` java
    /**
     * Parses {@code username} into a {@code Username} and returns it.
     * Leading and trailing whitespaces will be trimmed.
     * @throws IllegalValueException if the username does not meet length requirement and/or contains illegal characters
     */
    public static Username parseUsername(String username) throws IllegalValueException {
        requireNonNull(username);
        String trimmedUsername = username.trim();
        return new Username(trimmedUsername);
    }

    /**
     * Parses {@code password} into a {@code Password} and returns it.
     * Leading and trailing whitespaces will be trimmed.
     * @throws IllegalValueException if the password does not meet length requirement and/or contains illegal characters
     */
    public static Password parsePassword(String password) throws IllegalValueException {
        requireNonNull(password);
        String trimmedPassword = password.trim();
        return new Password(trimmedPassword);
    }

    /**
     * Parses a {@code Optional<String> totalDebt} into an {@code Optional<Debt>} if {@code totalDebt}
     * is present.
     * Meant for parsing for Edit command.
     * See header comment of this class regarding the use of {@code Optional} parameters.
     * @throws IllegalValueException if {@code totalDebt} is zero
     */
    public static Optional<Debt> parseTotalDebt(Optional<String> totalDebt) throws IllegalValueException {
        requireNonNull(totalDebt);
        if (totalDebt.isPresent() && Double.valueOf(totalDebt.get()) == 0) {
            throw new IllegalValueException(MESSAGE_INVALID_TOTAL_DEBT);
        }
        return totalDebt.isPresent() ? Optional.of(new Debt(totalDebt.get())) : Optional.empty();
    }
}
```
###### \java\seedu\address\logic\parser\PaybackCommandParser.java
``` java
/**
 * Parses input arguments and creates a new {@code PaybackCommand} object
 */
public class PaybackCommandParser implements Parser<PaybackCommand> {

    public static final String ONE_OR_MORE_SPACES_REGEX = "\\s+";
    // arguments: index and debt amount repaid
    private static final int MAXIMUM_ARGS_LENGTH = 2;
    private static final int ARGS_LENGTH_WITHOUT_INDEX = 1;


    @Override
    public PaybackCommand parse(String args) throws ParseException, CommandException {
        requireNonNull(args);

        Index index;
        Debt debtAmount;
        String[] argsList = args.trim().split(ONE_OR_MORE_SPACES_REGEX);
        if (argsList.length > MAXIMUM_ARGS_LENGTH) {
            throw new ParseException(String.format(MESSAGE_INVALID_COMMAND_FORMAT, PaybackCommand.MESSAGE_USAGE));
        }

        if (argsList.length == ARGS_LENGTH_WITHOUT_INDEX) {
            try {
                debtAmount = new Debt(argsList[0]);
            } catch (IllegalValueException ive) {
                throw new ParseException(String.format(MESSAGE_INVALID_COMMAND_FORMAT, PaybackCommand.MESSAGE_USAGE));
            }

            return new PaybackCommand(debtAmount);
        } else {
            try {
                index = ParserUtil.parseIndex(argsList[0]);
                debtAmount = new Debt(argsList[1]);
            } catch (IllegalValueException ive) {
                throw new ParseException(String.format(MESSAGE_INVALID_COMMAND_FORMAT, PaybackCommand.MESSAGE_USAGE));
            }
        }

        return new PaybackCommand(index, debtAmount);
    }
}
```
###### \java\seedu\address\logic\Password.java
``` java
/**
 * Represents the password of a user account.
 */
public class Password {
    public static final int PASSWORD_MIN_LENGTH = 6;
    public static final String MESSAGE_PASSWORD_CHARACTERS_CONSTRAINTS = "Password can only consist of uppercase "
            + "letters (A-Z), lowercase letters (a-z),"
            + " digits (0-9) and special characters (!@#$&()_-.+)";
    public static final String MESSAGE_PASSWORD_LENGTH_CONSTRAINTS = "Length of password cannot be less than "
            + PASSWORD_MIN_LENGTH + " characters";
    public static final String PASSWORD_VALIDATION_REGEX = "^[a-zA-Z0-9!@#$&()_\\-.+]+$";

    private String value;

    /**
     * Validates a given password.
     * @param value the password of the user
     * @throws IllegalValueException if given value string is invalid
     */
    public Password(String value) throws IllegalValueException {
        requireNonNull(value);
        String trimmedPassword = value.trim();
        if (!isValidPasswordLength(value)) {
            throw new IllegalValueException(MESSAGE_PASSWORD_LENGTH_CONSTRAINTS);
        }
        if (!hasValidPasswordCharacters(value)) {
            throw new IllegalValueException(MESSAGE_PASSWORD_CHARACTERS_CONSTRAINTS);
        }
        this.value = trimmedPassword;
    }

    /**
     * Checks if a given string has a valid password length.
     * @return {@code true} if the given string is a of a valid password length,
     * otherwise {@code false}
     */
    public static boolean isValidPasswordLength(String testVal) {
        return testVal.length() >= PASSWORD_MIN_LENGTH;
    }

    /**
     * Checks if a given string has valid password characters.
     * @return {@code true} if the given string contains valid password characters,
     * otherwise {@code false}
     */
    public static boolean hasValidPasswordCharacters(String testVal) {
        return testVal.matches(PASSWORD_VALIDATION_REGEX);
    }

    @Override
    public String toString() {
        return value;
    }

    @Override
    public boolean equals(Object other) {
        return other == this // short circuit if same object
                || (other instanceof Password // instanceof handles nulls
                && this.value.equals(((Password) other).value)); // state check
    }

}
```
###### \java\seedu\address\logic\Username.java
``` java
/**
 * Represents the username of a user account.
 */
public class Username {

    public static final int USERNAME_MIN_LENGTH = 6;
    public static final String MESSAGE_USERNAME_CHARACTERS_CONSTRAINTS = "Username can only consist of uppercase "
                                                                            + "letters (A-Z), lowercase letters (a-z),"
                                                                            + " digits (0-9) and underscores (_)";
    public static final String MESSAGE_USERNAME_LENGTH_CONSTRAINTS = "Length of username cannot be less than "
                                                                          + USERNAME_MIN_LENGTH + " characters";
    public static final String USERNAME_VALIDATION_REGEX = "^[a-zA-Z0-9_]+$";

    private String value;

    /**
     * Validates a given username.
     * @param value the username of the user
     * @throws IllegalValueException if the given {@code value} is invalid
     */
    public Username(String value) throws IllegalValueException {
        requireNonNull(value);
        String trimmedUsername = value.trim();
        if (!isValidUsernameLength(value)) {
            throw new IllegalValueException(MESSAGE_USERNAME_LENGTH_CONSTRAINTS);
        }
        if (!hasValidUsernameCharacters(value)) {
            throw new IllegalValueException(MESSAGE_USERNAME_CHARACTERS_CONSTRAINTS);
        }
        this.value = trimmedUsername;
    }

    /**
     * Checks if a given string has a valid username length.
     * @return {@code true} if the given string is a of a valid username length,
     * otherwise {@code false}
     */
    public static boolean isValidUsernameLength(String testVal) {
        return testVal.length() >= USERNAME_MIN_LENGTH;
    }

    /**
     * Checks if a given string has valid username characters.
     * @return {@code true} if the given string contains valid username characters,
     * otherwise {@code false}
     */
    public static boolean hasValidUsernameCharacters(String testVal) {
        return testVal.matches(USERNAME_VALIDATION_REGEX);
    }

    @Override
    public String toString() {
        return value;
    }

    @Override
    public boolean equals(Object other) {
        return other == this // short circuit if same object
                || (other instanceof Username // instanceof handles nulls
                && this.value.equals(((Username) other).value)); // state check
    }

}
```
###### \java\seedu\address\model\AddressBook.java
``` java
    /**
     * Increase debts of a person by the indicated amount
     * @param target person that borrowed more money
     * @param amount amount that the person borrowed. Must be either a positive integer or positive number with
     *               two decimal places
     * @throws PersonNotFoundException if {@code target} could not be found in the list.
     */
    public ReadOnlyPerson addDebtToPerson(ReadOnlyPerson target, Debt amount) throws PersonNotFoundException {
        Person editedPerson = new Person(target);

        try {
            Debt newCurrDebt = new Debt(target.getDebt().toNumber() + amount.toNumber());
            Debt newTotalDebt = new Debt(target.getTotalDebt().toNumber() + amount.toNumber());
            editedPerson.setDebt(newCurrDebt);
            editedPerson.setTotalDebt(newTotalDebt);
            editedPerson.setDateRepaid(new DateRepaid(NO_DATE_REPAID));
            persons.setPerson(target, editedPerson);
        } catch (DuplicatePersonException dpe) {
            assert false : "There should be no duplicate when updating the debt of a person";
        } catch (IllegalValueException ive) {
            assert false : "New debt amount should not be invalid since amount and debt field in target have "
                    + "been validated";
        }
        return editedPerson;
    }

    /**
     * Decrease the debt of a person by the amount indicated
     * @param target person in the address book who paid back some money
     * @param amount amount that the person paid back. Must be either a positive integer or positive number with
     *               two decimal places
     * @return ReadOnly editPerson
     * @throws PersonNotFoundException if {@code target} could not be found in the list.
     * @throws IllegalValueException if {@code amount} that is repaid by the person is more than the debt owed.
     */
    public ReadOnlyPerson deductDebtFromPerson(ReadOnlyPerson target, Debt amount) throws PersonNotFoundException,
            IllegalValueException {
        int index;
        index = persons.getIndexOf(target);

        Person editedPerson = new Person(target);
        double newDebtAmt = target.getDebt().toNumber() - amount.toNumber();

        if (newDebtAmt < 0) {
            throw new IllegalValueException(PaybackCommand.MESSAGE_PAYBACK_FAILURE);
        }

        try {
            Debt newDebt = new Debt(newDebtAmt);
            editedPerson.setDebt(newDebt);
            persons.setPerson(target, editedPerson);
        } catch (DuplicatePersonException dpe) {
            assert false : "There should be no duplicate when updating the debt of a person";
        } catch (IllegalValueException ive) {
            assert false : "New debt amount should not be invalid since amount and debt field in target have "
                    + "been validated";
        }

        return persons.getReadOnlyPerson(index);
    }

```
###### \java\seedu\address\model\ModelManager.java
``` java
    /**
     * Performs user authentication.
     * @throws UserNotFoundException if username and password does not match those in the user preference file
     * @throws IllegalValueException if username and password does not meet username and password requirements
     */
    public void authenticateUser(Username username, Password password) throws UserNotFoundException,
            IllegalValueException {
        Username fileUsername = new Username(getUsernameFromUserPref());
        String enteredPassword = password.toString();
        if (fileUsername.equals(username) && isSamePasswordAsUserPref(enteredPassword,
                userPrefs.getPasswordSalt())) {
            raise(new LoginAppRequestEvent(true));
        } else {
            raise(new LoginAppRequestEvent(false));
            throw new UserNotFoundException();
        }
    }

    /**
     * Logs user out.
     */
    public void logout() {
        deselectPerson();
        raise(new LoginAppRequestEvent(false));
        raise(new LogoutAppRequestEvent(true));
    }

    public String getUsernameFromUserPref() {
        return userPrefs.getAdminUsername();
    }

    public String getPasswordFromUserPref() {
        return userPrefs.getAdminPassword();
    }

    /**
     * Checks the entered password against the password stored in {@code UserPrefs} class.
     * @param currPassword password entered by user
     * @return true if the hash generated from the entered password matches the hashed password stored
     * in {@code UserPrefs}
     */
    public boolean isSamePasswordAsUserPref(String currPassword, byte[] salt) {
        String hashedPassword = getSha512SecurePassword(currPassword, salt);
        return hashedPassword.equals(userPrefs.getAdminPassword());
    }

    /**
     * Increases the debt of a person by the amount indicated.
     * @param target person in the address book who borrowed more money
     * @param amount amount that the person borrowed. Must be either a positive integer or positive number with
     *               two decimal places
     * @throws PersonNotFoundException if {@code target} could not be found in the list
     */
    @Override
    public ReadOnlyPerson addDebtToPerson(ReadOnlyPerson target, Debt amount) throws PersonNotFoundException {
        ReadOnlyPerson person = addressBook.addDebtToPerson(target, amount);
        indicateAddressBookChanged();
        return person;
    }

    /**
     * Decreases the debt of a person by the amount indicated.
     * @param target person in the address book who paid back some money
     * @param amount amount that the person paid back. Must be either a positive integer or positive number with
     *               two decimal places
     * @return the repaying person
     * @throws PersonNotFoundException if {@code target} could not be found in the list
     * @throws IllegalValueException if {@code amount} that is repaid by the person is more than the debt owed
     */
    @Override
    public ReadOnlyPerson deductDebtFromPerson(ReadOnlyPerson target, Debt amount) throws PersonNotFoundException,
            IllegalValueException {
        ReadOnlyPerson repayingPerson = addressBook.deductDebtFromPerson(target, amount);
        indicateAddressBookChanged();
        return repayingPerson;
    }

```
###### \java\seedu\address\model\ModelManager.java
``` java
    @Subscribe
    public void handleLoginAppRequestEvent(LoginAppRequestEvent event) {
        setCurrentListName("list");
        filteredPersons.setPredicate(PREDICATE_SHOW_ALL_PERSONS);
    }

```
###### \java\seedu\address\model\person\Person.java
``` java
    /**
     * Sets the total debt of a person to {@code totalDebt}.
     * @param totalDebt must not be null and cannot be less than current debt
     */
    public void setTotalDebt(Debt totalDebt) throws IllegalValueException {
        requireNonNull(totalDebt);
        if (totalDebt.toNumber() < debt.get().toNumber()) {
            throw new IllegalValueException(MESSAGE_INVALID_TOTAL_DEBT);
        }
        this.totalDebt.set(totalDebt);
    }

    @Override
    public ObjectProperty<Debt> totalDebtProperty() {
        return totalDebt;
    }

    @Override
    public Debt getTotalDebt() {
        return totalDebt.get();
    }
```
###### \java\seedu\address\model\person\PersonContainsTagPredicate.java
``` java
/**
 * Tests if a {@code Person}'s {@code Tag} matches any of the keywords given.
 */
public class PersonContainsTagPredicate implements Predicate<ReadOnlyPerson> {
    private final List<String> tagKeywords;

    public PersonContainsTagPredicate(List<String> tagKeywords) {
        this.tagKeywords = tagKeywords;
    }

    /**
     * Evaluates this predicate on the given {@code person}. This predicate tests if a person contains at least one tag
     * from {@code tagKeywords}.
     * @return {@code true} if the person matches the predicate,
     * otherwise {@code false}
     */
    @Override
    public boolean test(ReadOnlyPerson person) {
        Set<Tag> tagList = person.getTags();
        for (Tag tag : tagList) {
            if (tagKeywords.stream().anyMatch(keyword -> StringUtil.containsWordIgnoreCase(tag.tagName, keyword))) {
                return true;
            }
        }
        return false;
    }

    @Override
    public boolean equals(Object other) {
        return other == this // short circuit if same object
                || (other instanceof NameContainsKeywordsPredicate // instanceof handles nulls
                && this.tagKeywords.equals(((PersonContainsTagPredicate) other).tagKeywords)); // state check
    }
}
```
###### \java\seedu\address\ui\CommandBox.java
``` java
    /**
     * Masks password starting from the second whitespace(if it exists) until another whitespace is detected.
     */
    private void handlePasswordMasking() {
        String currentInput = commandTextField.getText();
        prevInputLength = inputLength;
        inputLength = commandTextField.getLength();
        indexOfFirstWhitespace = currentInput.indexOf(" ");
        indexOfSecondWhitespace = currentInput.indexOf(" ", indexOfFirstWhitespace + 1);
        currentMaskFromIndex = indexOfSecondWhitespace + 1;
        indexOfLastChar = inputLength - 1;

        // update index that indicates where to start masking from if the user deletes and re-enters password
        if (maskFromIndex == 0) {
            maskFromIndex = currentMaskFromIndex;
        }

        /*
         * mask password after the second whitespace and prevent the reading of the BLACK_CIRCLE after replacing
         * a character in the command box text field with a BLACK_CIRCLE
         */
        if (numOfSpaces >= 2 && currentInput.charAt(currentInput.length() - 1) != ' '
                && currentInput.charAt(currentInput.length() - 1) != BLACK_CIRCLE) {
            maskPasswordInput(currentInput);
        }

        if (!passwordFromInput.isEmpty() && inputLength < prevInputLength) {
            // case 1: user deletes password till just before second whitespace
            handleBackspaceEvent();
        } else if (passwordFromInput.isEmpty() && (indexOfLastChar - 1 != indexOfSecondWhitespace)) {
            // case 2: user deletes password including the second whitespace
            initialiseVariablesUsedInMasking();
        }
    }

    /**
     * Initialise the necessary variables for password masking.
     */
    private void initialiseVariablesUsedInMasking() {
        passwordFromInput = "";
        maskFromIndex = 0;
    }

    /**
     * Mask text field from second whitespace onwards.
     * @param currentInput the current text in the command box
     */
    private void maskPasswordInput(String currentInput) {
        // if user enters a new character, mask it with a BLACK_CIRCLE
        if (currentMaskFromIndex <= maskFromIndex && maskFromIndex <= inputLength) {
            passwordFromInput += commandTextField.getText(maskFromIndex, inputLength);
            StringBuilder maskedPasswordBuilder = new StringBuilder();
            for (int i = maskFromIndex; i < currentInput.length(); i++) {
                maskedPasswordBuilder.append(BLACK_CIRCLE);
            }
            commandTextField.replaceText(maskFromIndex, currentInput.length(), maskedPasswordBuilder.toString());
            maskFromIndex = currentInput.length();
        }
    }

    /**
     * Updates {@code passwordFromInput} field appropriately when user backspaces.
     */
    private void handleBackspaceEvent() {
        if (getNumOfSpaces(commandTextField.getText()) < 2) {
            initialiseVariablesUsedInMasking();
        } else {
            passwordFromInput = passwordFromInput.substring(0, inputLength - currentMaskFromIndex);
            maskFromIndex--;
        }
    }

    /**
     * Returns an integer that represents the number of whitespaces in a given string.
     */
    private int getNumOfSpaces(String currentInput) {
        int count = 0;
        for (int i = 0; i < currentInput.length(); i++) {
            if (currentInput.charAt(i) == ' ') {
                count++;
            }
        }
        return count;
    }


    /**
     * Handles the Enter button pressed event.
     */
    @FXML
    private void handleCommandInputChanged() {
        String commandText = commandTextField.getText();
        String trimmedCommandText = commandText.trim();
        if (trimmedCommandText.equals(LoginCommand.COMMAND_WORD) && !isLoggedIn()) {
            commandTextField.setText("");
            raise(new ChangeToLoginViewEvent());
            return;
        }

        if (isLoggedIn() && commandText.contains(LoginCommand.COMMAND_WORD)) {
            raise(new NewResultAvailableEvent(ALREADY_LOGGED_IN_MESSAGE, true));
        } else {
            // allow only help, exit and login commands to be executed before login
            boolean isCommandExecutableBeforeLogin = commandText.contains(LoginCommand.COMMAND_WORD)
                    || commandText.contains(ExitCommand.COMMAND_WORD)
                    || commandText.contains(HelpCommand.COMMAND_WORD);

            if (isCommandExecutableBeforeLogin || isLoggedIn()) {
                try {
                    executeAllowedCommands(commandText);
                } catch (CommandException | ParseException e) {
                    initHistory();
                    // handle command failure
                    setStyleToIndicateCommandFailure();
                    logger.info("Invalid command: " + commandText);
                    raise(new NewResultAvailableEvent(e.getMessage(), true));
                }
            } else {
                // commands that are not permitted before login including unknown commands
                setStyleToIndicateCommandFailure();
                raise(new NewResultAvailableEvent(LoginCommand.MESSAGE_LOGIN_REQUEST, true));
            }
        }
    }

    /**
     * Executes the correct command based on {@code commandInput}.
     * @throws CommandException when an error occurs during execution of a command
     * @throws ParseException when an error occurs during the parsing of command arguments
     */
    private void executeAllowedCommands(String commandInput) throws CommandException, ParseException {
        CommandResult commandResult;
        if (commandInput.contains(LoginCommand.COMMAND_WORD)) {
            commandResult = logic.execute(commandInput.substring(0,
                    indexOfSecondWhitespace + 1) + passwordFromInput);
        } else {
            commandResult = logic.execute(commandInput);
        }
        initHistory();
        historySnapshot.next();
        // process result of the command
        commandTextField.setText("");
        logger.info("Result: " + commandResult.feedbackToUser);
        raise(new NewResultAvailableEvent(commandResult.feedbackToUser, false));
    }
```
###### \java\seedu\address\ui\DebtRepaymentProgressBar.java
``` java
/**
 * Displays and sets the correct information to display in the debt repayment progress bar for each person.
 */
public class DebtRepaymentProgressBar extends UiPart<Region> {
    private static final String FXML = "DebtRepaymentProgressBar.fxml";
    private static final String COMPLETED_REPAYMENT_MESSAGE = "Completed";
    private static final String OVERDUE_REPAYMENT_MESSAGE = "Overdue";
    private static final String NO_DEADLINE_REPAYMENT_MESSAGE = "No deadline set";
    private static final String DEADLINE_TODAY_REPAYMENT_MESSAGE = "Today is the deadline";
    private static final String YEARS_LEFT_TO_REPAY_DEBT_MESSAGE = "%1$s years(s)";
    private static final String MONTHS_LEFT_TO_REPAY_DEBT_MESSAGE = " %1$s month(s)";
    private static final String DAYS_LEFT_TO_REPAY_DEBT_MESSAGE = " %1$s day(s)";
    private static final String COMPLETED_TEXT_ID = "completedText";
    private static final String NO_DEADLINE_TEXT_ID = "noDeadlineText";
    private static final String REPAYMENT_INFO_TEXT_ID = "repaymentInfoText";
    private ReadOnlyPerson person;
    private Double ratio;

    @FXML
    private ProgressBar progressBar;
    @FXML
    private Label percentage;
    @FXML
    private Label repaymentInfo;

    public DebtRepaymentProgressBar(ReadOnlyPerson person) {
        super(FXML);

        this.person = person;
        ratio = getFractionThatIsRepaid(person);
        progressBar.setProgress(ratio);
        bindListeners();
        progressBar.getStyleClass().clear();
        progressBar.getStyleClass().add("progress-bar");
        styleRepaymentProgressInfo();
        registerAsAnEventHandler(this);
    }

    /**
     * Calculates the amount that is repaid by a person in fraction.
     * @return the fraction of debt repaid over the total debt of a person
     */
    private Double getFractionThatIsRepaid(ReadOnlyPerson person) {
        Double totalDebt = person.getTotalDebt().toNumber();
        Double repaid = totalDebt - person.getDebt().toNumber();
        return repaid / totalDebt;
    }

    /**
     * Binds the individual UI elements to observe their respective {@code DebtRepaymentProgressBar} properties
     * so that they will be notified of any changes.
     */
    private void bindListeners() {
        repaymentInfo.textProperty().bind(Bindings.convert(getRepaymentStatus()));
        repaymentInfo.textProperty().addListener((unused1, unused2, unused3) -> {
            // style repaymentInfo whenever its value changes
            styleRepaymentProgressInfo();
        });
        percentage.textProperty().bind(Bindings.convert(getPercentage(ratio)));
        percentage.textProperty().addListener((unused1, unused2, unused3) -> {
            getRepaymentStatus();
        });
    }

    /**
     * Styles repayment information that is located next to the debt repayment progress bar.
     */
    private void styleRepaymentProgressInfo() {
        String repaymentInformation = repaymentInfo.getText();
        if (repaymentInformation.equals(COMPLETED_REPAYMENT_MESSAGE)) {
            repaymentInfo.setId(COMPLETED_TEXT_ID);
        } else if (repaymentInformation.equals(NO_DEADLINE_REPAYMENT_MESSAGE)) {
            repaymentInfo.setId(NO_DEADLINE_TEXT_ID);
        } else {
            repaymentInfo.setId(REPAYMENT_INFO_TEXT_ID);
        }
    }

    /**
     * Returns a {@code ObservableValue<String>} to bind to {@code percentage} property.
     */
    private ObservableValue<String> getPercentage(Double ratio) {
        String percentage = String.format("%.2f", ratio * 100);
        return new SimpleObjectProperty<>(percentage.concat("%"));
    }

    /**
     * Returns a {@code ObservableValue<String>} to bind to {@code repaymentInfo} property.
     */
    private ObservableValue<String> getRepaymentStatus() {
        Deadline deadline = person.getDeadline();
        if (person.getDebt().toNumber() == 0) {
            return new SimpleObjectProperty<>(COMPLETED_REPAYMENT_MESSAGE);
        }

        if (deadline.toString().equals(NO_DEADLINE_SET)) {
            return new SimpleObjectProperty<>(NO_DEADLINE_REPAYMENT_MESSAGE);
        }

        LocalDate deadlineDate = deadline.getDeadlineAsLocalDate();
        LocalDate today = LocalDate.now();

        if (Period.between(today, deadlineDate).isNegative()) {
            return new SimpleObjectProperty<>(OVERDUE_REPAYMENT_MESSAGE);
        } else {
            Period timeInterval = Period.between(today, deadlineDate);
            long years = timeInterval.getYears();
            long months = timeInterval.getMonths();
            long days = timeInterval.getDays();

            return new SimpleObjectProperty<>(formatTimeTillDeadline(years, months, days));
        }
    }

    /**
     * Formats the difference in time from now till a deadline into a {@code String}.
     * @param years years left till deadline
     * @param months months left till deadline
     * @param days days left till deadline
     * @return a formatted string that states the time difference between now and the deadline
     */
    private String formatTimeTillDeadline(long years, long months, long days) {
        StringBuilder stringBuilder = new StringBuilder();
        if (years == 0 && months == 0 && days == 0) {
            return DEADLINE_TODAY_REPAYMENT_MESSAGE;
        }
        if (years != 0) {
            stringBuilder.append(String.format(YEARS_LEFT_TO_REPAY_DEBT_MESSAGE, years));
        }
        if (months != 0) {
            stringBuilder.append(String.format(MONTHS_LEFT_TO_REPAY_DEBT_MESSAGE, months));
        }
        if (days != 0) {
            stringBuilder.append(String.format(DAYS_LEFT_TO_REPAY_DEBT_MESSAGE, days));
        }
        stringBuilder.append(" till deadline");
        return stringBuilder.toString();
    }
}
```
###### \java\seedu\address\ui\InfoPanel.java
``` java
    /**
     * Resets the debt repayment progress bar.
     * @param person the person whose person card is selected in the address book
     */
    private void resetDebtRepaymentProgressBar(ReadOnlyPerson person) {
        debtRepaymentProgressBar = new DebtRepaymentProgressBar(person);
        progressBarPlaceholder.getChildren().clear();
        progressBarPlaceholder.getChildren().add(debtRepaymentProgressBar.getRoot());
    }

```
###### \java\seedu\address\ui\InfoPanel.java
``` java
    /**
     * Sets all info fields to not display anything.
     */
    private void loadDefaultPage() {
        personInfoPanel.setVisible(false);
    }

```
###### \java\seedu\address\ui\LoginView.java
``` java
/**
 * The UI component that is responsible for receiving user login inputs.
 */
public class LoginView extends UiPart<Region> {
    public static final String SEPARATOR = "|";
    public static final String GUI_LOGIN_COMMAND_FORMAT = "login " + "%1$s" + SEPARATOR + "%2$s" + SEPARATOR;

    private static final String FXML = "LoginView.fxml";
    private static final Logger logger = LogsCenter.getLogger(LoginView.class);

    private static boolean showingLoginView = false;
    private final Logic logic;

    @FXML
    private TextField usernameField;
    @FXML
    private PasswordField passwordField;

    public LoginView(Logic logic) {
        super(FXML);
        this.logic = logic;
        logger.info("Showing login view...");
        addListeners();
    }

    /**
     * Adds listeners to {@code usernameField} and {@code passwordField}.
     */
    private void addListeners() {
        usernameField.textProperty().addListener((unused1, unused2, unused3) -> {
        });
        passwordField.textProperty().addListener((unused1, unused2, unused3) -> {
        });
    }

    /**
     * Handles the Enter button pressed event.
     */
    @FXML
    private void handleLoginInputChanged() {
        String usernameText = usernameField.getText();
        String passwordText = passwordField.getText();
        // process login inputs
        try {
            CommandResult commandResult;
            if (!containSeparatorString(usernameText) && !containSeparatorString(passwordText)) {
                commandResult = logic.execute(String.format(GUI_LOGIN_COMMAND_FORMAT, usernameText, passwordText));
                raise(new NewResultAvailableEvent(commandResult.feedbackToUser, false));
            } else if (containSeparatorString(usernameText)) {
                throw new ParseException(Username.MESSAGE_USERNAME_CHARACTERS_CONSTRAINTS);
            } else {
                throw new ParseException(Password.MESSAGE_PASSWORD_CHARACTERS_CONSTRAINTS);
            }
        } catch (CommandException | ParseException e) {
            raise(new NewResultAvailableEvent(e.getMessage(), true));
        }
        if (isLoggedIn()) {
            usernameField.setText("");
            passwordField.setText("");
        }
    }

    /**
     * Handles the button clicking event. Changes from the current login view to command box view.
     */
    @FXML
    private void handleBackToCommandView() {
        EventsCenter.getInstance().post(new ChangeToCommandBoxView());
    }

    public static void setShowingLoginView(boolean val) {
        showingLoginView = val;
    }

    public static boolean isShowingLoginView() {
        return showingLoginView;
    }

    /**
     * Checks if {@code input} contains {@code SEPARATOR} characters.
     * @return {@code true} if {@code input} contains {@code SEPARATOR},
     * otherwise {@code false}
     */
    private boolean containSeparatorString(String input) {
        return input.contains(SEPARATOR);
    }
}
```
###### \java\seedu\address\ui\MainWindow.java
``` java
    /**
     * Fills up all the placeholders of window once the app starts up.
     * Should only display welcome page without contacts.
     */
    void fillInnerPartsForStartUp() {
        startUpPanel = new StartUpPanel(primaryStage);
        infoPanelPlaceholder.getChildren().clear();
        infoPanelPlaceholder.getChildren().add(startUpPanel.getRoot());

        personListStartUpPanel = new PersonListStartUpPanel();
        personListPanelPlaceholder.getChildren().clear();
        personListPanelPlaceholder.getChildren().add(personListStartUpPanel.getRoot());

        ResultDisplay resultDisplay = new ResultDisplay();
        resultDisplayPlaceholder.getChildren().add(resultDisplay.getRoot());

        commandBox = new CommandBox(logic);
        commandBoxPlaceholder.getChildren().clear();
        commandBoxPlaceholder.getChildren().add(commandBox.getRoot());
    }

    /**
     * Fills up the placeholder of command box.
     */
    void fillInnerPartsForCommandBox() {
        commandBoxPlaceholder.getChildren().clear();
        commandBoxPlaceholder.getChildren().add(commandBox.getRoot());
    }

    /**
     * Changes from command box to login view with text fields for username and password.
     */
    public void fillCommandBoxWithLoginFields() {
        LoginView loginView = new LoginView(logic);
        commandBoxPlaceholder.getChildren().clear();
        commandBoxPlaceholder.getChildren().add(loginView.getRoot());
    }

```
###### \java\seedu\address\ui\PersonListStartUpPanel.java
``` java
/**
 * Empty panel to be displayed instead of PersonListPanel before login.
 * Users who have not logged in successfully should not be able to view the contacts in the address book.
 */
public class PersonListStartUpPanel extends UiPart<Region> {

    public static final String PERSON_LIST_START_UP_PANEL_ID = "#personListStartUpPane";

    private static final String FXML = "PersonListStartUpPanel.fxml";

    public PersonListStartUpPanel() {
        super(FXML);
    }
}
```
###### \java\seedu\address\ui\StartUpPanel.java
``` java
/**
 * The {@code StartUpPanel} will be loaded in place of the {@code InfoPanel} on start up.
 */
public class StartUpPanel extends UiPart<Region> {
    private static final String FXML = "StartUpPanel.fxml";

    @FXML
    private ImageView welcome;

    private final Logger logger = LogsCenter.getLogger(this.getClass());

    public StartUpPanel(Stage stage) {
        super(FXML);
        logger.info("Loading welcome page...");
        welcome.fitHeightProperty().bind(stage.heightProperty());
    }

}
```
###### \java\seedu\address\ui\UiManager.java
``` java
    /**
     * Handles login event.
     * Displays contacts in address book if login is successful.
     */
    @Subscribe
    public void handleLoginAppRequestEvent(LoginAppRequestEvent event) {
        // login is successful
        if (event.getLoginStatus() == true) {
            logger.info("Login successful");
            LoginCommand.setLoginStatus(true);
            // show address book
            Platform.runLater(() -> mainWindow.fillInnerParts());
        }
    }

    /**
     * Displays login view (which consists of username and password text fields) with buttons to navigate between
     * the login view and command box.
     */
    @Subscribe
    public void handleChangeToLoginViewEvent(ChangeToLoginViewEvent event) {
        // user wants to login
        logger.info(LogsCenter.getEventHandlingLogMessage(event));
        mainWindow.fillCommandBoxWithLoginFields();
    }

    /**
     * Handles logout event.
     * Displays login page when user logs out.
     */
    @Subscribe
    public void handleLogoutAppRequestEvent(LogoutAppRequestEvent event) {
        // logout is successful
        if (event.getLogoutStatus() == true) {
            logger.info("Logout successful");
            LoginCommand.setLoginStatus(false);
            //show login page
            mainWindow.fillInnerPartsForStartUp();
        }
    }

    /**
     * Changes from login view to command box view.
     */
    @Subscribe
    private void handleBackToCommandViewRequest(ChangeToCommandBoxView event) {
        logger.info("Showing command box...");
        logger.info(LogsCenter.getEventHandlingLogMessage(event));
        mainWindow.fillInnerPartsForCommandBox();
    }

```
###### \resources\view\DebtRepaymentProgressBar.fxml
``` fxml

<StackPane xmlns="http://javafx.com/javafx/8.0.111" xmlns:fx="http://javafx.com/fxml/1">
   <children>
      <ProgressBar fx:id="progressBar" prefHeight="40.0" prefWidth="250.0" progress="0.0" StackPane.alignment="CENTER_LEFT" />
      <Label id="percentageInfo" fx:id="percentage" text="\$percentage" textAlignment="CENTER" StackPane.alignment="CENTER_LEFT">
         <StackPane.margin>
            <Insets left="95.0" />
         </StackPane.margin>
      </Label>
      <Label fx:id="repaymentInfo" text="\\$repaymentInfo" StackPane.alignment="CENTER_LEFT">
         <StackPane.margin>
            <Insets left="270.0" />
         </StackPane.margin>
      </Label>
   </children>
</StackPane>
```
