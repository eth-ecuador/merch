An "Event" can be a online meet, workshop or any human group activity and can have an id, image, description.
An "User" is the user of the Baseapp or Base ecosystem represented by their wallet address.
A "Creator" is role that can a "User" assume when he is creating an event.
A "Merch" is an image representing a physical Merch item in digital format and can have an id, image, url.
A EAS (Ethereum Attestation Service) is the main proof attendace to an event.

A "Basic Merch" is Soulbound Token(SBT) that is always related to an EAS record.
A "Premium Merch" is a Companion ERC-721 Token that is always related to a "Basic Merch" EAS record.

An "User" can create an event.
An event must have a list of claim codes created and associated to it.
A "User" that proves him attendace to an event generating the EAS record as the proof of attendance and trigger automatically (at the moment of claim code submission thru the frontend) a new minted "Basic Merch" (the frontend choose a random image from a stack of preset images for "Basic Merch"), then the frontend must show the gallery of "Premium Merch" (COMPANION ERC-721 NFT) incentivizing the "User" to "purchase" it.

When the "Premium Token" is minted with Attestation does not create a new attestation, it must be related to the "Basic Token" 




1. CREATOR CREATES A MERCH
1.1 "Creator": upload image to the frontend
1.2 Frontend: Send the image to the backend thru the API endpoint /api/createMerch
1.3 Backend: Store the image in IPFS
1.4 Backend: Store the URL image in DB
1.5 Backend: Response to the Frontend OK


2. USER CREATE EVENT
1.1 "User": Upload an event image to the Frontend.
1.2 Frontnend: Upload the image to IPFS thru a backend endpoint
1.3 Backend: upload the image to Pinata
1.4 Backend: Receives IPFS URI "ipfs://bafkreixxx..." and send it as response to the Frontend.
1.5 Frontend: Calls the Smart Contract Event creation function.
1.6 Smart Contract: Request to the Backend endpoint the "Claim codes" generation.
1.7 Backend: Generate the requested quantity of claim codes.
1.8 Backend: Batch Insert a PostgreSQL
1.9 Backend: Response to the Smart Contract.
1.10 Smart contract: Emmit "Event" creation confirmation to the Frontend.
1.11 Frontend: Request "Claim codes" to Backend specifying the "Event" id.
1.12 Frontend: Show the codes to in the Frontend.


3. USER CLAIM MERCH

3.1 Usuario: Enter the "Claim code" to the Frontend
3.2 Frontend: Request mint with attestation to the Smart Contract. 
MerchManager->mintSBTWithAttestation(**image\_URL,** userAddress, ...)
3.3 Smart Contract: Request "Claim code" check to the Backend.
3.4 Backend: Check the "Claim code" in the DB.
3.5 Backend: generate ECDSA signature with walletAddress, eventId, tokenURI.
3.6 Backend: Mark the code as used.
3.7 Backend responde al smart contract.
3.8 Smart Contract: check signature.
3.9 Smart Contract: finalize the "Basic Merch" mint.
3.10 Frontend: Show the minted "Basic Merch"
3.11 Frontend: Show the list the Premium Merch (The images **uploaded by the Creators**)

OPTIONAL FLOW WHEN THE USER DECIDE TO GET PREMIUM MERCH

3.12 User: Click in the selected "Premium Merch" image to Purchase it.
3.13 Frontend: Call the "Premium Merch" function. mintCompanionWithAttestation(image\_url, userAddress, attestationId, eventId)
3.14 Smart contract: response to the frontend with ok.
3.15 Frontend: Shows the minted Premium NFT