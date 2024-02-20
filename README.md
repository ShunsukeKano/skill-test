# Yalantis - Skill Test

## Environment Setup

### Genral

- Used nvm to switch to Node version 16.20.2.

### Frontend

- In env.js, changed "export const SERVER_URL" to "http://localhost:5000/api/" to run locally (Backend uses 5000 port)

### Backend

- Utilized MAMP to run MySQL locally (HOST=localhost:8889, all environment variables are in "backend/.env").
- In db-connection.js, changed to host: process.env.HOST.split(":")[0], // "localhost" port: process.env.HOST.split(":")[1], // "8889"

### Minor Error Remediation

- In package.json, corrected "cd backend & npm start" to "cd backend && npm start" to run backend correctly.
- Installed a missing Node.js package.

## Task 1: User Registration and Login Issues

### 1. SQL Dump File Validation

Initially, I utilized "mgldefi.sql". However, this file lacked the "get_bnb" field, prompting me to switch to "new_mgldefi.sql".

### 2. Identifying and Correcting Errors

#### COLLATE=utf8mb4_0900_ai_ci

Replaced all instances of "COLLATE=utf8mb4_0900_ai_ci" with "COLLATE=utf8mb4_general_ci" to resolve compatibility issues.

#### SQL Syntax Correction

Adjusted SQL Syntax of "transaction": Changed

```sql
`to_admin` tinyint(1) NOT NULL
PRIMARY KEY (`id`) )
```

to

```sql
`to_admin` tinyint(1) NOT NULL,
  PRIMARY KEY (`id`)
```

### 3. Re-Importing the Database

Successfully re-imported the database using the modified dump file.

### 4. Fixed Email Configuration

- Used smtp2go.com
- In user.server.js and emailer.service.js, updated the configuration to

```JSX
var transport = nodemailer.createTransport({
            host: process.env.EMAIL_HOST,
            port: Number(process.env.EMAIL_PORT),
            auth: {
                user: process.env.EMAIL_USER,
                pass: process.env.EMAIL_PWD
            }
        });
```

### 5. Validated User Registration and Login

Both User Registration and Login are working correctly.

## Task 2: Displaying Current Ethereum Price on Profile Page

### 1. EthPriceCard.js Component

Created "EthPriceCard.js" component to display the Ethereum price.

```JSX
function EthPriceCard(props) {
    return (
              <div className="flex-grow text-black mb-10">
                <div className="pairCard flex justify-center py-4">
                  <div className="flex flex-col text-center lg:block lg:text-left">
                    <span className="text-xs font-bold xl:text-base 2xl:text-lg mr-2">Current ETH Price</span>
                    {/* <span  size="small" className="rounded-base font-bold text-red-500 ">{parseFloat(props.percent).toFixed(2)}%</span> */}
                    <p className="text-base font-bold xl:text-xl 2xl:text-3xl">$ {parseFloat(props.ethPrice).toFixed(2)}</p>
                    {/*<p className="text-xs  xl:text-base 2xl:text-lg">$ {props.amount2}</p>*/}
                  </div>
                </div>
              </div>
    );
  }

  export default EthPriceCard;
```

### 2. API Integration in Profile Page ("UserIngo/index")

Implemented API fetching from CoinGecko using "useEffect":

```JSX
const [ethPrice, setEthPrice] = useState(null);

const fetchEthPrice = async () => {
  try {
    const response = await fetch('https://api.coingecko.com/api/v3/simple/price?ids=ethereum&vs_currencies=usd');
    const data = await response.json();
    setEthPrice(data.ethereum.usd);
  } catch (error) {
    console.error('Error fetching ETH price:', error);
  }
};

useEffect(() => {
  fetchEthPrice();
}, []);
```

### 3. Passing Data via React Props

Passed the Ethereum price to the component using React props:

```JSX
{ethPrice && <EthPriceCard ethPrice={ethPrice}/>}
```
